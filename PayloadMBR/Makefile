BUILDDIR = Build

CC       = cc
PY       = python2
NASM     = nasm
QEMU     = qemu-system-i386

CCFLAGS  = 
ASFLAGS  = 

FRAMES   = $(sort $(wildcard Data/Image/Frames/*.png))
SPECIAL  = $(sort $(wildcard Data/Image/Special/*.png))

MUSIC    = Data/Song/nyan.mid

STAGE1ASM = $(wildcard Source/Stage1/*.asm)
STAGE2ASM = $(wildcard Source/Stage2/*.asm)


all: disk.img

dir: $(BUILDDIR)

$(BUILDDIR):
	mkdir -p $(BUILDDIR)

$(BUILDDIR)/frames.bin: $(FRAMES)
	$(PY) Data/Image/png2bin.py $(FRAMES) $@
	
$(BUILDDIR)/special.bin: $(SPECIAL)
	$(PY) Data/Image/png2bin.py $(SPECIAL) $@

$(BUILDDIR)/song.bin: $(MUSIC)
	$(PY) Data/Song/midi2bin.py $(MUSIC) $@

$(BUILDDIR)/compress: Source/Compressor/compress.c
	$(CC) Source/Compressor/compress.c -o $@ $(CCFLAGS)

$(BUILDDIR)/stage2.bin: $(STAGE2ASM) $(BUILDDIR)/song.bin $(BUILDDIR)/frames.bin $(BUILDDIR)/special.bin
	cd Source/Stage2 && $(NASM) main.asm -o ../../$@ $(ASFLAGS)
	
$(BUILDDIR)/stage2-compressed.bin: $(BUILDDIR)/stage2.bin $(BUILDDIR)/compress
	$(BUILDDIR)/compress $(BUILDDIR)/stage2.bin $@
	
disk.img: dir $(STAGE1ASM) $(BUILDDIR)/stage2-compressed.bin
	cd Source/Stage1 && $(NASM) main.asm -o ../../disk.img $(ASFLAGS)
	
test: disk.img
	$(QEMU) -s -soundhw pcspk -fda disk.img

clean:
	rm -r $(BUILDDIR)
	rm disk.img

.PHONY: test clean all dir