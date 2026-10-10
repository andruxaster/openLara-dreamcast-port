# -mno-fsca: GCC 15 (sh-elf) ICE in gen_reg_rtx when expanding fsca (TrapLavaEmitter::update)
OPTIMISE = -ffast-math -mpretend-cmove -fno-stack-protector -mno-fsca

DEBUG = 0

INCLUDES = ../.. ../dc

COMMONFLAGS = $(addprefix -I,$(INCLUDES)) $(OPTIMISE) -Wno-format -Wno-unused-variable -Wno-unused-but-set-variable -Wno-unused-value -Wno-sign-compare
# GCC 15 defaults to C23; this C code is written for gnu17
CFLAGS = $(COMMONFLAGS) -std=gnu17
CXXFLAGS = $(COMMONFLAGS) -fno-use-cxa-atexit -fno-exceptions -fno-rtti

TARGET := oldc-debug.elf

ifeq ($(DEBUG), 0)
COMMONFLAGS += -DNOSERIAL
TARGET := oldc-release.elf
endif

OBJS = main.o
#OBJS += ../../libs/stb_vorbis/stb_vorbis.o
OBJS += ../../libs/tinf/tinflate.o 
OBJS += dc_hardware.o
OBJS += audio.o aica.bin.o
OBJS += xmtrx.o xmtrx_c.o
OBJS += primitive/polygon.o primitive/modifier.o primitive/prim_buffer.o
OBJS += watchdog.o profile.o
OBJS += romdisk.o
KOS_ROMDISK_DIR = romdisk



all: $(TARGET)

$(TARGET): $(OBJS)
	kos-cc -o $(TARGET) $(OBJS) -lz -lm -lstdc++ $(LIBS)

main.o: ../../gapi/ta.h ../../*.h

primitive/modifier.o: primitive/primitive.h
primitive/polygon.o: primitive/primitive.h
primitive/prim_buffer.o: primitive/primitive.h

include $(KOS_BASE)/Makefile.rules

clean:
	-rm -f $(OBJS) $(TARGET)

rm-elf:
	-rm -f $(TARGET) romdisk.*

addr:
	sh-elf-addr2line -e $(TARGET) $(ARG)

run: $(TARGET)
	$(KOS_LOADER) $(TARGET) -i DATA.ISO

distclean:
	-rm -f *.CDI *.BIN *.elf ip.txt 1ST_READ.BIN

emu:
	/mnt/c/opt/demul/demul.exe -run=dc -image=TEST.CDI

data:
	mkisofs -U -D -o $< DATA.ISO $(HOME)/data

iso:
	mkdcdisc -e $(TARGET) -d $(HOME)/data/DATA -d $(HOME)/data/DELDATA -n "OpenLara" -a "histat" -N -o TEST.CDI

icon_data_2bpp:
	echo 'static unsigned char icon_data_2bpp[] = {' >> $@.tmp
	hexdump -v -s 9 -e '16/1 "0x%02x, " "\n"' \
	icon.pbm >> $@.tmp
	echo '		0 };' >> $@.tmp
