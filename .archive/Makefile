FILES = ./build/kernel.asm.o
LD=i686-elf-ld
CC=i686-elf-gcc
#LD=i686-w64-mingw32-ld
AS=i686-w64-mingw32-as
# CC=C:/msys64/mingw32/bin/i686-w64-mingw32-gcc.exe
#LD=C:/msys64/mingw32/bin/ld.exe
OBJCOPY=i686-w64-mingw32-objcopy

all: ./bin/boot.bin ./bin/kernel.bin
	rm -rf ./bin/os.bin
	dd if=./bin/boot.bin >> ./bin/os.bin
	dd if=./bin/kernel.bin >> ./bin/os.bin
	dd if=/dev/zero bs=512 count=100 >> ./bin/os.bin

./bin/kernel.bin: $(FILES)
 	$(LD) -g -relocatable $(FILES) -o ./build/kernelfull.o
	$(CC) -g -nostdlib -Wl,-r -o ./build/kernelfull.o $(FILES)
#	ld -g -r -o ./build/kernelfull.o $(FILES)
#	$(CC) -T ./src/linker.ld -o ./bin/kernel.bin -ffreestanding -O0 -nostdlib  ./build/kernelfull.o
#	$(LD) -T ./src/linker.ld -o ./bin/kernel.bin -ffreestanding -O0 -nostdlib -Wl,--oformat=binary  ./build/kernelfull.o
	$(CC) -T ./src/linker.ld -o ./build/kernel.elf -nostdlib ./build/kernelfull.o
# Convert ELF to binary
	$(OBJCOPY) -O binary ./build/kernel.elf ./bin/kernel.bin

./bin/boot.bin: ./src/boot/boot.asm
	nasm -f bin ./src/boot/boot.asm -o ./bin/boot.bin

./build/kernel.asm.o: ./src/kernel.asm
#	nasm -f elf -g ./src/kernel.asm -o ./build/kernel.asm.o
	nasm -f elf ./src/kernel.asm -o ./build/kernel.asm.o	

clean:
	rm -rf ./bin/boot.bin
	rm -rf ./bin/kernel.bin
	rm -rf ./bin/os.bin
	rm -rf ${FILES}
	rm -rf ./build/kernelfull.o
	rm -rf ./build/kernel.elf

