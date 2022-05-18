# WIDE

## CHALLENGE INFO

> We've received reports that Draeger has stashed a huge arsenal in the pocket dimension Flaggle Alpha. You've managed to smuggle a discarded access terminal to the Widely Inflated Dimension Editor from his headquarters, but the entry for the dimension has been encrypted. Can you make it inside and take control?

### Resources
- Downloadable part: `rev_wide.zip`


## Enumeration

```bash
$ unzip rev_wide.zip
Archive:  rev_wide.zip
   creating: rev_wide/
  inflating: rev_wide/wide
  inflating: rev_wide/db.ex

$ cd rev_wide

$ ll
total 20
-rw-r--r-- 1 hag hag  1260 May  9 12:58 db.ex
-rwxr-xr-x 1 hag hag 12992 May  9 12:58 wide

$ file wide
wide: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=13869bb7ce2c22f474b95ba21c9d7e9ff74ecc3f, not stripped

$ checksec wide
[*] '/home/hag/ctf/htb-cyber-apocalypse-2022/reversing/wide/rev_wide/wide'
    Arch:     amd64-64-little
    RELRO:    Full RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      PIE enabled

$ file db.ex
db.ex: Matlab v4 mat-file (little endian) , numeric, rows 1835627088, columns 29557
```

Let's run the `wide` executable:

```bash
$ ./wide
Usage: ./wide db.ex

$ ./wide db.ex
[*] Welcome user: kr4eq4L2$12xb, to the Widely Inflated Dimension Editor [*]
[*]    Serving your pocket dimension storage needs since 14,012.5 B      [*]
[*]                       Displaying Dimensions....                      [*]
[*]       Name       |              Code                |   Encrypted    [*]
[X] Primus           | people breathe variety practice  |                [*]
[X] Cheagaz          | scene control river importance   |                [*]
[X] Byenoovia        | fighting cast it parallel        |                [*]
[X] Cloteprea        | facing motor unusual heavy       |                [*]
[X] Maraqa           | stomach motion sale valuable     |                [*]
[X] Aidor            | feathers stream sides gate       |                [*]
[X] Flaggle Alpha    | admin secret power hidden        |       *        [*]
Which dimension would you like to examine? Primus
Our home dimension
Which dimension would you like to examine? Cheagaz
Our home dimension
Which dimension would you like to examine? Flaggle Alpha
Our home dimension
Which dimension would you like to examine? power
Our home dimension
Which dimension would you like to examine? hidden
Our home dimension
Which dimension would you like to examine? aidor
Our home dimension
Which dimension would you like to examine? Aidor
Our home dimension
Which dimension would you like to examine?
Our home dimension
Which dimension would you like to examine? a
Our home dimension
Which dimension would you like to examine? ^C
```

Not sure what input this program expects... Let's try again:

```bash
$ ./wide db.ex
[*] Welcome user: kr4eq4L2$12xb, to the Widely Inflated Dimension Editor [*]
[*]    Serving your pocket dimension storage needs since 14,012.5 B      [*]
[*]                       Displaying Dimensions....                      [*]
[*]       Name       |              Code                |   Encrypted    [*]
[X] Primus           | people breathe variety practice  |                [*]
[X] Cheagaz          | scene control river importance   |                [*]
[X] Byenoovia        | fighting cast it parallel        |                [*]
[X] Cloteprea        | facing motor unusual heavy       |                [*]
[X] Maraqa           | stomach motion sale valuable     |                [*]
[X] Aidor            | feathers stream sides gate       |                [*]
[X] Flaggle Alpha    | admin secret power hidden        |       *        [*]
Which dimension would you like to examine? 1
The Ice Dimension
Which dimension would you like to examine? 2
The Berserk Dimension
Which dimension would you like to examine? 3
The Hungry Dimension
Which dimension would you like to examine? 4
The Water Dimension
Which dimension would you like to examine? 5
The Bone Dimension
Which dimension would you like to examine? 6
[X] That entry is encrypted - please enter your WIDE decryption key: 111
[X]                          Key was incorrect                           [X]
Which dimension would you like to examine? ^C
```

So the input should be numeric. And I guess it is zero based, because number 6 corresponds to `Flaggle Alpha` which is the 7th option.

We can also see that the `Flaggle Alpha` entry is encrypted and needs a decryption key to be read. I guess this entry will give us the flag if we can provide the correct key.

I guess we should look at the `db.ex` file to see how it is structured. I have a feeling it is not actually a `Matlab v4`-file as reported by the `file` command earlier:

> ```
> $ file db.ex
> db.ex: Matlab v4 mat-file (little endian) , numeric, rows 1835627088, columns 29557
> ```

Let's have a closer look:

```bash
$ cat db.ex
Primuspeople breathe variety practiceOur home dimensionCheagazscene control river importanceThe Ice DimensionByenooviafighting cast it parallelThe Berserk DimensionClotepreafacing motor unusual heavyThe Hungry DimensionMaraqastomach motion sale valuableThe Water DimensionAidorfeathers stream sides gateThe Bone DimensionFlaggle Alphaadmin s-Ց���#P

$ xxd db.ex
00000000: 0000 0000 5072 696d 7573 0000 0000 0000  ....Primus......
00000010: 0000 0000 7065 6f70 6c65 2062 7265 6174  ....people breat
00000020: 6865 2076 6172 6965 7479 2070 7261 6374  he variety pract
00000030: 6963 6500 4f75 7220 686f 6d65 2064 696d  ice.Our home dim
00000040: 656e 7369 6f6e 0000 0000 0000 0000 0000  ension..........
00000050: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000060: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000070: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000080: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000090: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000000a0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000000b0: 0000 0000 0000 0000 4368 6561 6761 7a00  ........Cheagaz.
000000c0: 0000 0000 0000 0000 7363 656e 6520 636f  ........scene co
000000d0: 6e74 726f 6c20 7269 7665 7220 696d 706f  ntrol river impo
000000e0: 7274 616e 6365 0000 5468 6520 4963 6520  rtance..The Ice
000000f0: 4469 6d65 6e73 696f 6e00 0000 0000 0000  Dimension.......
00000100: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000110: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000120: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000130: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000140: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000150: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000160: 0000 0000 0000 0000 0000 0000 4279 656e  ............Byen
00000170: 6f6f 7669 6100 0000 0000 0000 6669 6768  oovia.......figh
00000180: 7469 6e67 2063 6173 7420 6974 2070 6172  ting cast it par
00000190: 616c 6c65 6c00 0000 0000 0000 5468 6520  allel.......The
000001a0: 4265 7273 6572 6b20 4469 6d65 6e73 696f  Berserk Dimensio
000001b0: 6e00 0000 0000 0000 0000 0000 0000 0000  n...............
000001c0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000001d0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000001e0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000001f0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000200: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000210: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000220: 436c 6f74 6570 7265 6100 0000 0000 0000  Cloteprea.......
00000230: 6661 6369 6e67 206d 6f74 6f72 2075 6e75  facing motor unu
00000240: 7375 616c 2068 6561 7679 0000 0000 0000  sual heavy......
00000250: 5468 6520 4875 6e67 7279 2044 696d 656e  The Hungry Dimen
00000260: 7369 6f6e 0000 0000 0000 0000 0000 0000  sion............
00000270: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000280: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000290: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000002a0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000002b0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000002c0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000002d0: 0000 0000 4d61 7261 7161 0000 0000 0000  ....Maraqa......
000002e0: 0000 0000 7374 6f6d 6163 6820 6d6f 7469  ....stomach moti
000002f0: 6f6e 2073 616c 6520 7661 6c75 6162 6c65  on sale valuable
00000300: 0000 0000 5468 6520 5761 7465 7220 4469  ....The Water Di
00000310: 6d65 6e73 696f 6e00 0000 0000 0000 0000  mension.........
00000320: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000330: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000340: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000350: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000360: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000370: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000380: 0000 0000 0000 0000 4169 646f 7200 0000  ........Aidor...
00000390: 0000 0000 0000 0000 6665 6174 6865 7273  ........feathers
000003a0: 2073 7472 6561 6d20 7369 6465 7320 6761   stream sides ga
000003b0: 7465 0000 0000 0000 5468 6520 426f 6e65  te......The Bone
000003c0: 2044 696d 656e 7369 6f6e 0000 0000 0000   Dimension......
000003d0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000003e0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000003f0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000400: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000410: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000420: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000430: 0000 0000 0000 0000 0100 0000 466c 6167  ............Flag
00000440: 676c 6520 416c 7068 6100 0000 6164 6d69  gle Alpha...admi
00000450: 6e20 7365 6372 6574 2070 6f77 6572 2068  n secret power h
00000460: 6964 6465 6e00 0000 0000 0000 484f 742a  idden.......HOt*
00000470: 1ff3 d08c b694 7c75 7112 48f8 c593 d36f  ......|uq.H....o
00000480: 690d 2d1c d591 b3b8 c723 5000 0000 0000  i.-......#P.....
00000490: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000004a0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000004b0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000004c0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000004d0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000004e0: 0000 0000 0000 0000 0000 0000            ............
```

Ok, that should be helpful, now let's have a look at the executable itself. Let's use `r2`:

```bash
$ r2 wide
Warning: run r2 with -e bin.cache=true to fix relocations in disassembly
[0x000008e0]> aaaaaa
[x] Analyze all flags starting with sym. and entry0 (aa)
[x] Analyze function calls (aac)
[x] Analyze len bytes of instructions for references (aar)
[x] Finding and parsing C++ vtables (avrr)
[x] Type matching analysis for all functions (aaft)
[x] Propagate noreturn information (aanr)
[x] Finding function preludes
[x] Enable constraint types analysis for variables
[0x000008e0]> aaaaaaa
An r2 developer is coming to your place to manually analyze this program. Please wait for it

--press any key--
[0x000008e0]> afl
0x000008e0    1 43           entry0
0x00000910    4 50   -> 40   sym.deregister_tm_clones
0x00000950    4 66   -> 57   sym.register_tm_clones
0x000009a0    5 58   -> 51   sym.__do_global_dtors_aux
0x000008d0    1 6            sym.imp.__cxa_finalize
0x000009e0    1 10           entry.init0
0x00001070    1 2            sym.__libc_csu_fini
0x000009ea   15 1016         sym.menu
0x00001074    1 9            sym._fini
0x00001000    4 101          sym.__libc_csu_init
0x00000de2   11 536          main
0x000007d0    3 23           sym._init
0x00000800    1 6            sym.imp.wcscmp
0x00000810    1 6            sym.imp.puts
0x00000820    1 6            sym.imp.fread
0x00000830    1 6            sym.imp.mbstowcs
0x00000840    1 6            sym.imp.fclose
0x00000850    1 6            sym.imp.printf
0x00000000    3 97   -> 123  loc.imp._ITM_deregisterTMCloneTable
0x00000860    1 6            sym.imp.fgets
0x00000870    1 6            sym.imp.calloc
0x00000880    1 6            sym.imp.ftell
0x00000890    1 6            sym.imp.strtol
0x000008a0    1 6            sym.imp.fseek
0x000008b0    1 6            sym.imp.fopen
0x000008c0    1 6            sym.imp.exit
[0x000008e0]> pdf@main
            ; DATA XREF from entry0 @ 0x8fd
┌ 536: int main (signed int64_t argc, char **argv);
│           ; var char **filename @ rbp-0x30
│           ; var signed int64_t var_24h @ rbp-0x24
│           ; var int64_t var_20h @ rbp-0x20
│           ; var size_t nmeb @ rbp-0x1c
│           ; var file*stream @ rbp-0x18
│           ; var long size @ rbp-0x10
│           ; var file*ptr @ rbp-0x8
│           ; arg signed int64_t argc @ rdi
│           ; arg char **argv @ rsi
│           0x00000de2      55             push rbp
│           0x00000de3      4889e5         mov rbp, rsp
│           0x00000de6      4883ec30       sub rsp, 0x30
│           0x00000dea      897ddc         mov dword [var_24h], edi    ; argc
│           0x00000ded      488975d0       mov qword [filename], rsi   ; argv
│           0x00000df1      837ddc01       cmp dword [var_24h], 1
│       ┌─< 0x00000df5      7f25           jg 0xe1c
│       │   0x00000df7      488b45d0       mov rax, qword [filename]
│       │   0x00000dfb      488b00         mov rax, qword [rax]
│       │   0x00000dfe      4889c6         mov rsi, rax
│       │   0x00000e01      488d3d9d0300.  lea rdi, str.Usage:__s_db.ex_n ; 0x11a5 ; "Usage: %s db.ex\n" ; const char *format
│       │   0x00000e08      b800000000     mov eax, 0
│       │   0x00000e0d      e83efaffff     call sym.imp.printf         ; int printf(const char *format)
│       │   0x00000e12      bfffffffff     mov edi, 0xffffffff         ; -1
│       │   0x00000e17      e8a4faffff     call sym.imp.exit
│       │   ; CODE XREF from main @ 0xdf5
│       └─> 0x00000e1c      488d3d950300.  lea rdi, str.___Welcome_user:_kr4eq4L2_12xb__to_the_Widely_Inflated_Dimension_Editor___ ; 0x11b8 ; "[*] Welcome user: kr4eq4L2$12xb, to the Widely Inflated Dimension Editor [*]" ; const char *s
│           0x00000e23      e8e8f9ffff     call sym.imp.puts           ; int puts(const char *s)
│           0x00000e28      488d3dd90300.  lea rdi, str.______Serving_your_pocket_dimension_storage_needs_since_14_012.5_B________ ; 0x1208 ; "[*]    Serving your pocket dimension storage needs since 14,012.5 B      [*]" ; const char *s
│           0x00000e2f      e8dcf9ffff     call sym.imp.puts           ; int puts(const char *s)
│           0x00000e34      488b45d0       mov rax, qword [filename]
│           0x00000e38      4883c008       add rax, 8
│           0x00000e3c      488b00         mov rax, qword [rax]
│           0x00000e3f      488d350f0400.  lea rsi, [0x00001255]       ; "r" ; const char *mode
│           0x00000e46      4889c7         mov rdi, rax                ; const char *filename
│           0x00000e49      e862faffff     call sym.imp.fopen          ; file*fopen(const char *filename, const char *mode)
│           0x00000e4e      488945e8       mov qword [stream], rax
│           0x00000e52      48837de800     cmp qword [stream], 0
│       ┌─< 0x00000e57      7516           jne 0xe6f
│       │   0x00000e59      488d3df80300.  lea rdi, str._x__There_was_a_problem_accessing_your_database__x_ ; 0x1258 ; "[x] There was a problem accessing your database [x]" ; const char *s
│       │   0x00000e60      e8abf9ffff     call sym.imp.puts           ; int puts(const char *s)
│       │   0x00000e65      bfffffffff     mov edi, 0xffffffff         ; -1
│       │   0x00000e6a      e851faffff     call sym.imp.exit
│       │   ; CODE XREF from main @ 0xe57
│       └─> 0x00000e6f      488b45e8       mov rax, qword [stream]
│           0x00000e73      ba02000000     mov edx, 2                  ; int whence
│           0x00000e78      be00000000     mov esi, 0                  ; long offset
│           0x00000e7d      4889c7         mov rdi, rax                ; FILE *stream
│           0x00000e80      e81bfaffff     call sym.imp.fseek          ; int fseek(FILE *stream, long offset, int whence)
│           0x00000e85      488b45e8       mov rax, qword [stream]
│           0x00000e89      4889c7         mov rdi, rax                ; FILE *stream
│           0x00000e8c      e8eff9ffff     call sym.imp.ftell          ; long ftell(FILE *stream)
│           0x00000e91      488945f0       mov qword [size], rax
│           0x00000e95      488b45e8       mov rax, qword [stream]
│           0x00000e99      ba00000000     mov edx, 0                  ; int whence
│           0x00000e9e      be00000000     mov esi, 0                  ; long offset
│           0x00000ea3      4889c7         mov rdi, rax                ; FILE *stream
│           0x00000ea6      e8f5f9ffff     call sym.imp.fseek          ; int fseek(FILE *stream, long offset, int whence)
│           0x00000eab      488b75f0       mov rsi, qword [size]
│           0x00000eaf      488b4df0       mov rcx, qword [size]
│           0x00000eb3      48ba832dd882.  movabs rdx, 0x2d82d82d82d82d83
│           0x00000ebd      4889c8         mov rax, rcx
│           0x00000ec0      48f7e2         mul rdx
│           0x00000ec3      4889d0         mov rax, rdx
│           0x00000ec6      48c1e805       shr rax, 5
│           0x00000eca      4869c0b40000.  imul rax, rax, 0xb4
│           0x00000ed1      4829c1         sub rcx, rax
│           0x00000ed4      4889c8         mov rax, rcx
│           0x00000ed7      4829c6         sub rsi, rax
│           0x00000eda      4889f0         mov rax, rsi
│           0x00000edd      488945f0       mov qword [size], rax
│           0x00000ee1      488b45f0       mov rax, qword [size]
│           0x00000ee5      48ba832dd882.  movabs rdx, 0x2d82d82d82d82d83
│           0x00000eef      48f7e2         mul rdx
│           0x00000ef2      4889d0         mov rax, rdx
│           0x00000ef5      48c1e805       shr rax, 5
│           0x00000ef9      8945e4         mov dword [nmeb], eax
│           0x00000efc      8b45e4         mov eax, dword [nmeb]
│           0x00000eff      4898           cdqe
│           0x00000f01      beb4000000     mov esi, 0xb4               ; size_t size
│           0x00000f06      4889c7         mov rdi, rax                ; size_t nmeb
│           0x00000f09      e862f9ffff     call sym.imp.calloc         ; void *calloc(size_t nmeb, size_t size)
│           0x00000f0e      488945f8       mov qword [ptr], rax
│           0x00000f12      8b45e4         mov eax, dword [nmeb]
│           0x00000f15      4863d0         movsxd rdx, eax             ; size_t nmemb
│           0x00000f18      488b4de8       mov rcx, qword [stream]     ; FILE *stream
│           0x00000f1c      488b45f8       mov rax, qword [ptr]
│           0x00000f20      beb4000000     mov esi, 0xb4               ; size_t size
│           0x00000f25      4889c7         mov rdi, rax                ; void *ptr
│           0x00000f28      e8f3f8ffff     call sym.imp.fread          ; size_t fread(void *ptr, size_t size, size_t nmemb, FILE *stream)
│           0x00000f2d      488b45e8       mov rax, qword [stream]
│           0x00000f31      4889c7         mov rdi, rax                ; FILE *stream
│           0x00000f34      e807f9ffff     call sym.imp.fclose         ; int fclose(FILE *stream)
│           0x00000f39      488d3d500300.  lea rdi, str._________________________Displaying_Dimensions....________________________ ; 0x1290 ; "[*]                       Displaying Dimensions....                      [*]" ; const char *s
│           0x00000f40      e8cbf8ffff     call sym.imp.puts           ; int puts(const char *s)
│           0x00000f45      488d3d940300.  lea rdi, str._________Name_____________________Code___________________Encrypted______ ; 0x12e0 ; "[*]       Name       |              Code                |   Encrypted    [*]" ; const char *s
│           0x00000f4c      e8bff8ffff     call sym.imp.puts           ; int puts(const char *s)
│           0x00000f51      c745e0000000.  mov dword [var_20h], 0
│       ┌─< 0x00000f58      eb7c           jmp 0xfd6
│       │   ; CODE XREF from main @ 0xfdc
│      ┌──> 0x00000f5a      8b45e0         mov eax, dword [var_20h]
│      ╎│   0x00000f5d      4898           cdqe
│      ╎│   0x00000f5f      4869d0b40000.  imul rdx, rax, 0xb4
│      ╎│   0x00000f66      488b45f8       mov rax, qword [ptr]
│      ╎│   0x00000f6a      4801d0         add rax, rdx
│      ╎│   0x00000f6d      8b00           mov eax, dword [rax]
│      ╎│   0x00000f6f      85c0           test eax, eax
│     ┌───< 0x00000f71      7407           je 0xf7a
│     │╎│   0x00000f73      be2a000000     mov esi, 0x2a               ; '*'
│    ┌────< 0x00000f78      eb05           jmp 0xf7f
│    ││╎│   ; CODE XREF from main @ 0xf71
│    │└───> 0x00000f7a      be20000000     mov esi, 0x20               ; "@"
│    │ ╎│   ; CODE XREF from main @ 0xf78
│    └────> 0x00000f7f      8b45e0         mov eax, dword [var_20h]
│      ╎│   0x00000f82      4898           cdqe
│      ╎│   0x00000f84      4869d0b40000.  imul rdx, rax, 0xb4
│      ╎│   0x00000f8b      488b45f8       mov rax, qword [ptr]
│      ╎│   0x00000f8f      4801d0         add rax, rdx
│      ╎│   0x00000f92      488d5014       lea rdx, [rax + 0x14]
│      ╎│   0x00000f96      8b45e0         mov eax, dword [var_20h]
│      ╎│   0x00000f99      4898           cdqe
│      ╎│   0x00000f9b      4869c8b40000.  imul rcx, rax, 0xb4
│      ╎│   0x00000fa2      488b45f8       mov rax, qword [ptr]
│      ╎│   0x00000fa6      4801c8         add rax, rcx
│      ╎│   0x00000fa9      4883c004       add rax, 4
│      ╎│   0x00000fad      4c8d0d790300.  lea r9, [0x0000132d]
│      ╎│   0x00000fb4      4189f0         mov r8d, esi
│      ╎│   0x00000fb7      488d0d6f0300.  lea rcx, [0x0000132d]
│      ╎│   0x00000fbe      4889c6         mov rsi, rax
│      ╎│   0x00000fc1      488d3d680300.  lea rdi, str._X____16s____32s___6s_c_7s____n ; 0x1330 ; "[X] %-16s | %-32s | %6s%c%7s [*]\n" ; const char *format
│      ╎│   0x00000fc8      b800000000     mov eax, 0
│      ╎│   0x00000fcd      e87ef8ffff     call sym.imp.printf         ; int printf(const char *format)
│      ╎│   0x00000fd2      8345e001       add dword [var_20h], 1
│      ╎│   ; CODE XREF from main @ 0xf58
│      ╎└─> 0x00000fd6      8b45e0         mov eax, dword [var_20h]
│      ╎    0x00000fd9      3b45e4         cmp eax, dword [nmeb]
│      └──< 0x00000fdc      0f8c78ffffff   jl 0xf5a
│           0x00000fe2      8b55e4         mov edx, dword [nmeb]
│           0x00000fe5      488b45f8       mov rax, qword [ptr]
│           0x00000fe9      89d6           mov esi, edx                ; int64_t arg2
│           0x00000feb      4889c7         mov rdi, rax                ; int64_t arg1
│           0x00000fee      e8f7f9ffff     call sym.menu
│           0x00000ff3      b800000000     mov eax, 0
│           0x00000ff8      c9             leave
└           0x00000ff9      c3             ret
[0x000008e0]> pdf@sym.menu
            ; CALL XREF from main @ 0xfee
┌ 1016: sym.menu (file*arg1, size_t arg2);
│           ; var size_t var_1dch @ rbp-0x1dc
│           ; var file*var_1d8h @ rbp-0x1d8
│           ; var int64_t var_1cch @ rbp-0x1cc
│           ; var int64_t var_1c8h @ rbp-0x1c8
│           ; var long var_1c4h @ rbp-0x1c4
│           ; var wchar_t *pwcs @ rbp-0x1c0
│           ; var char *var_180h @ rbp-0x180
│           ; var int64_t var_178h @ rbp-0x178
│           ; var int64_t var_170h @ rbp-0x170
│           ; var int64_t var_168h @ rbp-0x168
│           ; var int64_t var_160h @ rbp-0x160
│           ; var int64_t var_158h @ rbp-0x158
│           ; var int64_t var_150h @ rbp-0x150
│           ; var int64_t var_14ch @ rbp-0x14c
│           ; var int64_t var_148h @ rbp-0x148
│           ; var int64_t var_144h @ rbp-0x144
│           ; var int64_t var_140h @ rbp-0x140
│           ; var int64_t var_13ch @ rbp-0x13c
│           ; var int64_t var_138h @ rbp-0x138
│           ; var int64_t var_134h @ rbp-0x134
│           ; var int64_t var_130h @ rbp-0x130
│           ; var int64_t var_12ch @ rbp-0x12c
│           ; var int64_t var_128h @ rbp-0x128
│           ; var int64_t var_124h @ rbp-0x124
│           ; var int64_t var_120h @ rbp-0x120
│           ; var int64_t var_11ch @ rbp-0x11c
│           ; var int64_t var_118h @ rbp-0x118
│           ; var int64_t var_114h @ rbp-0x114
│           ; var int64_t var_110h @ rbp-0x110
│           ; var int64_t var_10ch @ rbp-0x10c
│           ; var int64_t var_108h @ rbp-0x108
│           ; var int64_t var_104h @ rbp-0x104
│           ; var int64_t var_100h @ rbp-0x100
│           ; var int64_t var_fch @ rbp-0xfc
│           ; var int64_t var_f8h @ rbp-0xf8
│           ; var int64_t var_f4h @ rbp-0xf4
│           ; var int64_t var_f0h @ rbp-0xf0
│           ; var int64_t var_ech @ rbp-0xec
│           ; var int64_t var_e8h @ rbp-0xe8
│           ; var int64_t var_e4h @ rbp-0xe4
│           ; var int64_t var_e0h @ rbp-0xe0
│           ; var int64_t var_dch @ rbp-0xdc
│           ; var int64_t var_d8h @ rbp-0xd8
│           ; var int64_t var_d4h @ rbp-0xd4
│           ; var int64_t var_d0h @ rbp-0xd0
│           ; var char *s @ rbp-0xc0
│           ; var char *str @ rbp-0xb0
│           ; var int64_t var_a8h @ rbp-0xa8
│           ; var int64_t var_a0h @ rbp-0xa0
│           ; var int64_t var_98h @ rbp-0x98
│           ; var char *var_90h @ rbp-0x90
│           ; var int64_t var_88h @ rbp-0x88
│           ; var int64_t var_80h @ rbp-0x80
│           ; var int64_t var_78h @ rbp-0x78
│           ; var int64_t var_70h @ rbp-0x70
│           ; var int64_t var_68h @ rbp-0x68
│           ; var int64_t var_60h @ rbp-0x60
│           ; var int64_t var_58h @ rbp-0x58
│           ; var int64_t var_50h @ rbp-0x50
│           ; var int64_t var_48h @ rbp-0x48
│           ; var int64_t var_40h @ rbp-0x40
│           ; var int64_t var_38h @ rbp-0x38
│           ; var int64_t var_30h @ rbp-0x30
│           ; var int64_t var_28h @ rbp-0x28
│           ; var int64_t var_20h @ rbp-0x20
│           ; var int64_t var_18h @ rbp-0x18
│           ; var int64_t var_8h @ rbp-0x8
│           ; arg file*arg1 @ rdi
│           ; arg size_t arg2 @ rsi
│           0x000009ea      55             push rbp
│           0x000009eb      4889e5         mov rbp, rsp
│           0x000009ee      4881ece00100.  sub rsp, 0x1e0
│           0x000009f5      4889bd28feff.  mov qword [var_1d8h], rdi   ; arg1
│           0x000009fc      89b524feffff   mov dword [var_1dch], esi   ; arg2
│           0x00000a02      64488b042528.  mov rax, qword fs:[0x28]
│           0x00000a0b      488945f8       mov qword [var_8h], rax
│           0x00000a0f      31c0           xor eax, eax
│           0x00000a11      48c78550ffff.  mov qword [str], 0
│           0x00000a1c      48c78558ffff.  mov qword [var_a8h], 0
│           0x00000a27      48c78560ffff.  mov qword [var_a0h], 0
│           0x00000a32      48c78568ffff.  mov qword [var_98h], 0
│           0x00000a3d      c78538feffff.  mov dword [var_1c8h], 0
│           ; CODE XREFS from sym.menu @ 0xab5, 0xdb4, 0xdc5, 0xddd
│    ┌┌┌┌─> 0x00000a47      488d3d3a0600.  lea rdi, str.Which_dimension_would_you_like_to_examine__ ; 0x1088 ; "Which dimension would you like to examine? " ; const char *format
│    ╎╎╎╎   0x00000a4e      b800000000     mov eax, 0
│    ╎╎╎╎   0x00000a53      e8f8fdffff     call sym.imp.printf         ; int printf(const char *format)
│    ╎╎╎╎   0x00000a58      488b15b11520.  mov rdx, qword [obj.stdin]  ; obj.__TMC_END__
│    ╎╎╎╎                                                              ; [0x202010:8]=0 ; FILE *stream
│    ╎╎╎╎   0x00000a5f      488d8550ffff.  lea rax, [str]
│    ╎╎╎╎   0x00000a66      be20000000     mov esi, 0x20               ; "@" ; int size
│    ╎╎╎╎   0x00000a6b      4889c7         mov rdi, rax                ; char *s
│    ╎╎╎╎   0x00000a6e      e8edfdffff     call sym.imp.fgets          ; char *fgets(char *s, int size, FILE *stream)
│    ╎╎╎╎   0x00000a73      488d8550ffff.  lea rax, [str]
│    ╎╎╎╎   0x00000a7a      ba0a000000     mov edx, 0xa                ; int base
│    ╎╎╎╎   0x00000a7f      be00000000     mov esi, 0                  ; char * *endptr
│    ╎╎╎╎   0x00000a84      4889c7         mov rdi, rax                ; const char *str
│    ╎╎╎╎   0x00000a87      e804feffff     call sym.imp.strtol         ; long strtol(const char *str, char * *endptr, int base)
│    ╎╎╎╎   0x00000a8c      89853cfeffff   mov dword [var_1c4h], eax
│    ╎╎╎╎   0x00000a92      83bd3cfeffff.  cmp dword [var_1c4h], 0
│   ┌─────< 0x00000a99      780e           js 0xaa9
│   │╎╎╎╎   0x00000a9b      8b853cfeffff   mov eax, dword [var_1c4h]
│   │╎╎╎╎   0x00000aa1      3b8524feffff   cmp eax, dword [var_1dch]
│  ┌──────< 0x00000aa7      7c0e           jl 0xab7
│  ││╎╎╎╎   ; CODE XREF from sym.menu @ 0xa99
│  │└─────> 0x00000aa9      488d3d040600.  lea rdi, str.That_option_was_invalid. ; 0x10b4 ; "That option was invalid." ; const char *s
│  │ ╎╎╎╎   0x00000ab0      e85bfdffff     call sym.imp.puts           ; int puts(const char *s)
│  │ └────< 0x00000ab5      eb90           jmp 0xa47
│  │  ╎╎╎   ; CODE XREF from sym.menu @ 0xaa7
│  └──────> 0x00000ab7      8b853cfeffff   mov eax, dword [var_1c4h]
│     ╎╎╎   0x00000abd      4898           cdqe
│     ╎╎╎   0x00000abf      4869d0b40000.  imul rdx, rax, 0xb4
│     ╎╎╎   0x00000ac6      488b8528feff.  mov rax, qword [var_1d8h]
│     ╎╎╎   0x00000acd      4801d0         add rax, rdx
│     ╎╎╎   0x00000ad0      488b10         mov rdx, qword [rax]
│     ╎╎╎   0x00000ad3      488b4808       mov rcx, qword [rax + 8]
│     ╎╎╎   0x00000ad7      48899580feff.  mov qword [var_180h], rdx
│     ╎╎╎   0x00000ade      48898d88feff.  mov qword [var_178h], rcx
│     ╎╎╎   0x00000ae5      488b5010       mov rdx, qword [rax + 0x10]
│     ╎╎╎   0x00000ae9      488b4818       mov rcx, qword [rax + 0x18]
│     ╎╎╎   0x00000aed      48899590feff.  mov qword [var_170h], rdx
│     ╎╎╎   0x00000af4      48898d98feff.  mov qword [var_168h], rcx
│     ╎╎╎   0x00000afb      488b5020       mov rdx, qword [rax + 0x20]
│     ╎╎╎   0x00000aff      488b4828       mov rcx, qword [rax + 0x28]
│     ╎╎╎   0x00000b03      488995a0feff.  mov qword [var_160h], rdx
│     ╎╎╎   0x00000b0a      48898da8feff.  mov qword [var_158h], rcx
│     ╎╎╎   0x00000b11      488b5030       mov rdx, qword [rax + 0x30]
│     ╎╎╎   0x00000b15      488b4838       mov rcx, qword [rax + 0x38]
│     ╎╎╎   0x00000b19      488995b0feff.  mov qword [var_150h], rdx
│     ╎╎╎   0x00000b20      48898db8feff.  mov qword [var_148h], rcx
│     ╎╎╎   0x00000b27      488b5040       mov rdx, qword [rax + 0x40]
│     ╎╎╎   0x00000b2b      488b4848       mov rcx, qword [rax + 0x48]
│     ╎╎╎   0x00000b2f      488995c0feff.  mov qword [var_140h], rdx
│     ╎╎╎   0x00000b36      48898dc8feff.  mov qword [var_138h], rcx
│     ╎╎╎   0x00000b3d      488b5050       mov rdx, qword [rax + 0x50]
│     ╎╎╎   0x00000b41      488b4858       mov rcx, qword [rax + 0x58]
│     ╎╎╎   0x00000b45      488995d0feff.  mov qword [var_130h], rdx
│     ╎╎╎   0x00000b4c      48898dd8feff.  mov qword [var_128h], rcx
│     ╎╎╎   0x00000b53      488b5060       mov rdx, qword [rax + 0x60]
│     ╎╎╎   0x00000b57      488b4868       mov rcx, qword [rax + 0x68]
│     ╎╎╎   0x00000b5b      488995e0feff.  mov qword [var_120h], rdx
│     ╎╎╎   0x00000b62      48898de8feff.  mov qword [var_118h], rcx
│     ╎╎╎   0x00000b69      488b5070       mov rdx, qword [rax + 0x70]
│     ╎╎╎   0x00000b6d      488b4878       mov rcx, qword [rax + 0x78]
│     ╎╎╎   0x00000b71      488995f0feff.  mov qword [var_110h], rdx
│     ╎╎╎   0x00000b78      48898df8feff.  mov qword [var_108h], rcx
│     ╎╎╎   0x00000b7f      488b90800000.  mov rdx, qword [rax + 0x80]
│     ╎╎╎   0x00000b86      488b88880000.  mov rcx, qword [rax + 0x88]
│     ╎╎╎   0x00000b8d      48899500ffff.  mov qword [var_100h], rdx
│     ╎╎╎   0x00000b94      48898d08ffff.  mov qword [var_f8h], rcx
│     ╎╎╎   0x00000b9b      488b90900000.  mov rdx, qword [rax + 0x90]
│     ╎╎╎   0x00000ba2      488b88980000.  mov rcx, qword [rax + 0x98]
│     ╎╎╎   0x00000ba9      48899510ffff.  mov qword [var_f0h], rdx
│     ╎╎╎   0x00000bb0      48898d18ffff.  mov qword [var_e8h], rcx
│     ╎╎╎   0x00000bb7      488b90a00000.  mov rdx, qword [rax + 0xa0]
│     ╎╎╎   0x00000bbe      488b88a80000.  mov rcx, qword [rax + 0xa8]
│     ╎╎╎   0x00000bc5      48899520ffff.  mov qword [var_e0h], rdx
│     ╎╎╎   0x00000bcc      48898d28ffff.  mov qword [var_d8h], rcx
│     ╎╎╎   0x00000bd3      8b80b0000000   mov eax, dword [rax + 0xb0]
│     ╎╎╎   0x00000bd9      898530ffffff   mov dword [var_d0h], eax
│     ╎╎╎   0x00000bdf      8b8580feffff   mov eax, dword [var_180h]
│     ╎╎╎   0x00000be5      85c0           test eax, eax
│    ┌────< 0x00000be7      0f84dd010000   je 0xdca
│    │╎╎╎   0x00000bed      488b85b4feff.  mov rax, qword [var_14ch]
│    │╎╎╎   0x00000bf4      488b95bcfeff.  mov rdx, qword [var_144h]
│    │╎╎╎   0x00000bfb      48898570ffff.  mov qword [var_90h], rax
│    │╎╎╎   0x00000c02      48899578ffff.  mov qword [var_88h], rdx
│    │╎╎╎   0x00000c09      488b85c4feff.  mov rax, qword [var_13ch]
│    │╎╎╎   0x00000c10      488b95ccfeff.  mov rdx, qword [var_134h]
│    │╎╎╎   0x00000c17      48894580       mov qword [var_80h], rax
│    │╎╎╎   0x00000c1b      48895588       mov qword [var_78h], rdx
│    │╎╎╎   0x00000c1f      488b85d4feff.  mov rax, qword [var_12ch]
│    │╎╎╎   0x00000c26      488b95dcfeff.  mov rdx, qword [var_124h]
│    │╎╎╎   0x00000c2d      48894590       mov qword [var_70h], rax
│    │╎╎╎   0x00000c31      48895598       mov qword [var_68h], rdx
│    │╎╎╎   0x00000c35      488b85e4feff.  mov rax, qword [var_11ch]
│    │╎╎╎   0x00000c3c      488b95ecfeff.  mov rdx, qword [var_114h]
│    │╎╎╎   0x00000c43      488945a0       mov qword [var_60h], rax
│    │╎╎╎   0x00000c47      488955a8       mov qword [var_58h], rdx
│    │╎╎╎   0x00000c4b      488b85f4feff.  mov rax, qword [var_10ch]
│    │╎╎╎   0x00000c52      488b95fcfeff.  mov rdx, qword [var_104h]
│    │╎╎╎   0x00000c59      488945b0       mov qword [var_50h], rax
│    │╎╎╎   0x00000c5d      488955b8       mov qword [var_48h], rdx
│    │╎╎╎   0x00000c61      488b8504ffff.  mov rax, qword [var_fch]
│    │╎╎╎   0x00000c68      488b950cffff.  mov rdx, qword [var_f4h]
│    │╎╎╎   0x00000c6f      488945c0       mov qword [var_40h], rax
│    │╎╎╎   0x00000c73      488955c8       mov qword [var_38h], rdx
│    │╎╎╎   0x00000c77      488b8514ffff.  mov rax, qword [var_ech]
│    │╎╎╎   0x00000c7e      488b951cffff.  mov rdx, qword [var_e4h]
│    │╎╎╎   0x00000c85      488945d0       mov qword [var_30h], rax
│    │╎╎╎   0x00000c89      488955d8       mov qword [var_28h], rdx
│    │╎╎╎   0x00000c8d      488b8524ffff.  mov rax, qword [var_dch]
│    │╎╎╎   0x00000c94      488b952cffff.  mov rdx, qword [var_d4h]
│    │╎╎╎   0x00000c9b      488945e0       mov qword [var_20h], rax
│    │╎╎╎   0x00000c9f      488955e8       mov qword [var_18h], rdx
│    │╎╎╎   0x00000ca3      488d3d260400.  lea rdi, str._X__That_entry_is_encrypted___please_enter_your_WIDE_decryption_key:_ ; 0x10d0 ; "[X] That entry is encrypted - please enter your WIDE decryption key: " ; const char *format
│    │╎╎╎   0x00000caa      b800000000     mov eax, 0
│    │╎╎╎   0x00000caf      e89cfbffff     call sym.imp.printf         ; int printf(const char *format)
│    │╎╎╎   0x00000cb4      488b15551320.  mov rdx, qword [obj.stdin]  ; obj.__TMC_END__
│    │╎╎╎                                                              ; [0x202010:8]=0 ; FILE *stream
│    │╎╎╎   0x00000cbb      488d8540ffff.  lea rax, [s]
│    │╎╎╎   0x00000cc2      be10000000     mov esi, 0x10               ; int size
│    │╎╎╎   0x00000cc7      4889c7         mov rdi, rax                ; char *s
│    │╎╎╎   0x00000cca      e891fbffff     call sym.imp.fgets          ; char *fgets(char *s, int size, FILE *stream)
│    │╎╎╎   0x00000ccf      488d8d40ffff.  lea rcx, [s]
│    │╎╎╎   0x00000cd6      488d8540feff.  lea rax, [pwcs]
│    │╎╎╎   0x00000cdd      ba10000000     mov edx, 0x10               ; size_t n
│    │╎╎╎   0x00000ce2      4889ce         mov rsi, rcx                ; const char *s
│    │╎╎╎   0x00000ce5      4889c7         mov rdi, rax                ; wchar_t *pwcs
│    │╎╎╎   0x00000ce8      e843fbffff     call sym.imp.mbstowcs       ; size_t mbstowcs(wchar_t *pwcs, const char *s, size_t n)
│    │╎╎╎   0x00000ced      488d8540feff.  lea rax, [pwcs]
│    │╎╎╎   0x00000cf4      488d351d0400.  lea rsi, str.sup3rs3cr3tw1d3 ; 0x1118 ; U"sup3rs3cr3tw1d3" ; const wchar_t *s2
│    │╎╎╎   0x00000cfb      4889c7         mov rdi, rax                ; const wchar_t *s1
│    │╎╎╎   0x00000cfe      e8fdfaffff     call sym.imp.wcscmp         ; int wcscmp(const wchar_t *s1, const wchar_t *s2)
│    │╎╎╎   0x00000d03      85c0           test eax, eax
│   ┌─────< 0x00000d05      0f85ae000000   jne 0xdb9
│   ││╎╎╎   0x00000d0b      c78534feffff.  mov dword [var_1cch], 0
│  ┌──────< 0x00000d15      eb7c           jmp 0xd93
│  │││╎╎╎   ; CODE XREF from sym.menu @ 0xd9c
│ ┌───────> 0x00000d17      8b8534feffff   mov eax, dword [var_1cch]
│ ╎│││╎╎╎   0x00000d1d      4898           cdqe
│ ╎│││╎╎╎   0x00000d1f      0fb6840570ff.  movzx eax, byte [rbp + rax - 0x90]
│ ╎│││╎╎╎   0x00000d27      84c0           test al, al
│ ────────< 0x00000d29      7479           je 0xda4
│ ╎│││╎╎╎   0x00000d2b      8b8534feffff   mov eax, dword [var_1cch]
│ ╎│││╎╎╎   0x00000d31      4898           cdqe
│ ╎│││╎╎╎   0x00000d33      0fb6b40570ff.  movzx esi, byte [rbp + rax - 0x90]
│ ╎│││╎╎╎   0x00000d3b      8b9534feffff   mov edx, dword [var_1cch]
│ ╎│││╎╎╎   0x00000d41      89d0           mov eax, edx
│ ╎│││╎╎╎   0x00000d43      01c0           add eax, eax
│ ╎│││╎╎╎   0x00000d45      01d0           add eax, edx
│ ╎│││╎╎╎   0x00000d47      8d14c5000000.  lea edx, [rax*8]
│ ╎│││╎╎╎   0x00000d4e      01c2           add edx, eax
│ ╎│││╎╎╎   0x00000d50      4863c2         movsxd rax, edx
│ ╎│││╎╎╎   0x00000d53      4869c0818080.  imul rax, rax, 0xffffffff80808081
│ ╎│││╎╎╎   0x00000d5a      48c1e820       shr rax, 0x20
│ ╎│││╎╎╎   0x00000d5e      01d0           add eax, edx
│ ╎│││╎╎╎   0x00000d60      c1f807         sar eax, 7
│ ╎│││╎╎╎   0x00000d63      89c1           mov ecx, eax
│ ╎│││╎╎╎   0x00000d65      89d0           mov eax, edx
│ ╎│││╎╎╎   0x00000d67      c1f81f         sar eax, 0x1f
│ ╎│││╎╎╎   0x00000d6a      29c1           sub ecx, eax
│ ╎│││╎╎╎   0x00000d6c      89c8           mov eax, ecx
│ ╎│││╎╎╎   0x00000d6e      89c1           mov ecx, eax
│ ╎│││╎╎╎   0x00000d70      c1e108         shl ecx, 8
│ ╎│││╎╎╎   0x00000d73      29c1           sub ecx, eax
│ ╎│││╎╎╎   0x00000d75      89d0           mov eax, edx
│ ╎│││╎╎╎   0x00000d77      29c8           sub eax, ecx
│ ╎│││╎╎╎   0x00000d79      31c6           xor esi, eax
│ ╎│││╎╎╎   0x00000d7b      89f2           mov edx, esi
│ ╎│││╎╎╎   0x00000d7d      8b8534feffff   mov eax, dword [var_1cch]
│ ╎│││╎╎╎   0x00000d83      4898           cdqe
│ ╎│││╎╎╎   0x00000d85      88940570ffff.  mov byte [rbp + rax - 0x90], dl
│ ╎│││╎╎╎   0x00000d8c      838534feffff.  add dword [var_1cch], 1
│ ╎│││╎╎╎   ; CODE XREF from sym.menu @ 0xd15
│ ╎└──────> 0x00000d93      8b8534feffff   mov eax, dword [var_1cch]
│ ╎ ││╎╎╎   0x00000d99      83f87f         cmp eax, 0x7f
│ └───────< 0x00000d9c      0f8675ffffff   jbe 0xd17
│  ┌──────< 0x00000da2      eb01           jmp 0xda5
│  │││╎╎╎   ; CODE XREF from sym.menu @ 0xd29
│ ────────> 0x00000da4      90             nop
│  │││╎╎╎   ; CODE XREF from sym.menu @ 0xda2
│  └──────> 0x00000da5      488d8570ffff.  lea rax, [var_90h]
│   ││╎╎╎   0x00000dac      4889c7         mov rdi, rax                ; const char *s
│   ││╎╎╎   0x00000daf      e85cfaffff     call sym.imp.puts           ; int puts(const char *s)
│   ││└───< 0x00000db4      e98efcffff     jmp 0xa47
│   ││ ╎╎   ; CODE XREF from sym.menu @ 0xd05
│   └─────> 0x00000db9      488d3d980300.  lea rdi, str._X___________________________Key_was_incorrect____________________________X_ ; 0x1158 ; "[X]
   Key was incorrect                           [X]" ; const char *s
│    │ ╎╎   0x00000dc0      e84bfaffff     call sym.imp.puts           ; int puts(const char *s)
│    │ └──< 0x00000dc5      e97dfcffff     jmp 0xa47
│    │  ╎   ; CODE XREF from sym.menu @ 0xbe7
│    └────> 0x00000dca      488d8580feff.  lea rax, [var_180h]
│       ╎   0x00000dd1      4883c034       add rax, 0x34
│       ╎   0x00000dd5      4889c7         mov rdi, rax                ; const char *s
│       ╎   0x00000dd8      e833faffff     call sym.imp.puts           ; int puts(const char *s)
└       └─< 0x00000ddd      e965fcffff     jmp 0xa47
[0x000008e0]>
```

Ok, that's a lot of output, but the interesting part can be found in the `sym.menu` function which we disassembled using `pdf@sym.menu`. Here's the important parts again:

```bash
│    │╎╎╎   0x00000ca3      488d3d260400.  lea rdi, str._X__That_entry_is_encrypted___please_enter_your_WIDE_decryption_key:_ ; 0x10d0 ; "[X] That entry is encrypted - please enter your WIDE decryption key: " ; const char *format
│    │╎╎╎   0x00000caa      b800000000     mov eax, 0
│    │╎╎╎   0x00000caf      e89cfbffff     call sym.imp.printf         ; int printf(const char *format)
│    │╎╎╎   0x00000cb4      488b15551320.  mov rdx, qword [obj.stdin]  ; obj.__TMC_END__
│    │╎╎╎                                                              ; [0x202010:8]=0 ; FILE *stream
│    │╎╎╎   0x00000cbb      488d8540ffff.  lea rax, [s]
│    │╎╎╎   0x00000cc2      be10000000     mov esi, 0x10               ; int size
│    │╎╎╎   0x00000cc7      4889c7         mov rdi, rax                ; char *s
│    │╎╎╎   0x00000cca      e891fbffff     call sym.imp.fgets          ; char *fgets(char *s, int size, FILE *stream)
│    │╎╎╎   0x00000ccf      488d8d40ffff.  lea rcx, [s]
│    │╎╎╎   0x00000cd6      488d8540feff.  lea rax, [pwcs]
│    │╎╎╎   0x00000cdd      ba10000000     mov edx, 0x10               ; size_t n
│    │╎╎╎   0x00000ce2      4889ce         mov rsi, rcx                ; const char *s
│    │╎╎╎   0x00000ce5      4889c7         mov rdi, rax                ; wchar_t *pwcs
│    │╎╎╎   0x00000ce8      e843fbffff     call sym.imp.mbstowcs       ; size_t mbstowcs(wchar_t *pwcs, const char *s, size_t n)
│    │╎╎╎   0x00000ced      488d8540feff.  lea rax, [pwcs]
│    │╎╎╎   0x00000cf4      488d351d0400.  lea rsi, str.sup3rs3cr3tw1d3 ; 0x1118 ; U"sup3rs3cr3tw1d3" ; const wchar_t *s2
│    │╎╎╎   0x00000cfb      4889c7         mov rdi, rax                ; const wchar_t *s1
│    │╎╎╎   0x00000cfe      e8fdfaffff     call sym.imp.wcscmp         ; int wcscmp(const wchar_t *s1, const wchar_t *s2)
```

This is the part that checks the decryption key and we can clearly see that the key is compared to `sup3rs3cr3tw1d3`.

The reason that this string isn't easily found using `strings` or even `xxd` is because it is a wide string (notice the `wchar_t` type). The name of the executable is a pretty big hint. We also see calls to `mbstowcs` and `wcscmp` which specifically handles wide strings.

```bash
$ ./wide db.ex
[*] Welcome user: kr4eq4L2$12xb, to the Widely Inflated Dimension Editor [*]
[*]    Serving your pocket dimension storage needs since 14,012.5 B      [*]
[*]                       Displaying Dimensions....                      [*]
[*]       Name       |              Code                |   Encrypted    [*]
[X] Primus           | people breathe variety practice  |                [*]
[X] Cheagaz          | scene control river importance   |                [*]
[X] Byenoovia        | fighting cast it parallel        |                [*]
[X] Cloteprea        | facing motor unusual heavy       |                [*]
[X] Maraqa           | stomach motion sale valuable     |                [*]
[X] Aidor            | feathers stream sides gate       |                [*]
[X] Flaggle Alpha    | admin secret power hidden        |       *        [*]
Which dimension would you like to examine? 6
[X] That entry is encrypted - please enter your WIDE decryption key: sup3rs3cr3tw1d3
HTB{str1ngs_4r3nt_4lw4ys_4sc11}
Which dimension would you like to examine? Our home dimension
Which dimension would you like to examine? ^C
```

## Flag

`HTB{str1ngs_4r3nt_4lw4ys_4sc11}`
