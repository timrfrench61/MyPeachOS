# MyPeachOS

build: 
make clean
make

run:
qemu-system-x86_64 -hda ./boot.bin

run in GDB linux :
gdb
target remote | qemu-system-x86_64 -hda ./boot.bin -S -gdb stdio
debug:
gdb
<load symbols>
break <breakpoint>
qemu-system-x86_64 -hda ./boot.bin -S -gdb stdio

run in windows :
cd bin
qemu-system-x86_64 -hda ./boot.bin -S -gdb tcp::1234
(open another command prompt)
gdb
(gdb) target remote localhost:1234
(gdb) c ;continue
(gdb) ctrl-c
(gdb) layout asm
...
debug session
...
(gdb) quit
