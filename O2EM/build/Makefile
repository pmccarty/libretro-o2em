DEBUG := 0

include ./makefile_common.mk

ifeq ($(platform),)
platform = unix
ifeq ($(shell uname -a),)
   platform = win
else ifneq ($(findstring MINGW,$(shell uname -a)),)
   platform = win
else ifneq ($(findstring Darwin,$(shell uname -a)),)
   platform = osx
else ifneq ($(findstring win,$(shell uname -a)),)
   platform = win
endif
endif

ifeq ($(platform), android)
   CC = arm-linux-androideabi-gcc
   AR = @arm-linux-androideabi-ar
   LD = @arm-linux-androideabi-g++ 
   TARGET := libretro-o2em.so
   fpic := -fPIC
   SHARED :=  -Wl,--fix-cortex-a8 -llog -lz -shared -Wl,--version-script=$(LIBRETRO)/link.T -Wl,--no-undefined
   PLATFLAGS :=   -DRETRO -DAND -DLSB_FIRST -DALIGN_DWORD  -D__LIBRETRO__
else ifeq ($(platform), unix)
   CC = gcc
   TARGET := libretro-o2em.so
   fpic := -fPIC
   SHARED :=   -shared -Wl,--version-script=$(LIBRETRO)/link.T -Wl,--no-undefined
   PLATFLAGS :=  -DRETRO -DLSB_FIRST -DALIGN_DWORD  -D__LIBRETRO__
else ifeq ($(platform), osx)
   TARGET := libretro.dylib
   fpic := -fPIC
   SHARED := -dynamiclib
   PLATFLAGS :=  -DRETRO -DLSB_FIRST -DALIGN_DWORD  -D__LIBRETRO__
else ifeq ($(platform), wii)
   TARGET := hatari_libretro_wii.a
   CC = $(DEVKITPPC)/bin/powerpc-eabi-gcc$(EXE_EXT)
   CXX = $(DEVKITPPC)/bin/powerpc-eabi-g++$(EXE_EXT)
   AR = $(DEVKITPPC)/bin/powerpc-eabi-ar$(EXE_EXT)   
   ZLIB_DIR = ../sources/utils/zlib/
   ZLIB_OBJECTS	:= $(ZLIB_DIR)adler32.o $(ZLIB_DIR)compress.o $(ZLIB_DIR)crc32.o $(ZLIB_DIR)deflate.o \
		$(ZLIB_DIR)gzclose.o $(ZLIB_DIR)gzlib.o $(ZLIB_DIR)gzread.o\
		$(ZLIB_DIR)gzwrite.o $(ZLIB_DIR)infback.o $(ZLIB_DIR)inffast.o $(ZLIB_DIR)inflate.o \
		$(ZLIB_DIR)trees.o $(ZLIB_DIR)inftrees.o $(ZLIB_DIR)uncompr.o $(ZLIB_DIR)uncompr.o $(ZLIB_DIR)zutil.o 
   CFLAGS += -DSDL_BYTEORDER=SDL_BIG_ENDIAN -DMSB_FIRST -DBYTE_ORDER=BIG_ENDIAN  -DBYTE_ORDER=BIG_ENDIAN \
	-DWIIPORT=1 -DHAVE_MEMALIGN -DHAVE_ASPRINTF -I$(ZLIB_DIR) -I$(DEVKITPRO)/libogc/include \
	-D__powerpc__ -D__POWERPC__ -DGEKKO -DHW_RVL -mrvl -mcpu=750 -meabi -mhard-float -D__ppc__
   SHARED :=   -lm -lpthread -lc
   PLATFLAGS :=  -DRETRO -DALIGN_DWORD -DWIIPORT
else ifeq ($(platform), ps3)
   TARGET := hatari_libretro_ps3.a
   CC = $(CELL_SDK)/host-win32/ppu/bin/ppu-lv2-gcc.exe
   CXX = $(CELL_SDK)/host-win32/ppu/bin/ppu-lv2-g++.exe
   AR = $(CELL_SDK)/host-win32/ppu/bin/ppu-lv2-ar.exe
   ZLIB_DIR = ../sources/utils/zlib/
   ZLIB_OBJECTS	:= $(ZLIB_DIR)adler32.o $(ZLIB_DIR)compress.o $(ZLIB_DIR)crc32.o $(ZLIB_DIR)deflate.o \
		$(ZLIB_DIR)gzclose.o $(ZLIB_DIR)gzlib.o $(ZLIB_DIR)gzread.o\
		$(ZLIB_DIR)gzwrite.o $(ZLIB_DIR)infback.o $(ZLIB_DIR)inffast.o $(ZLIB_DIR)inflate.o \
		$(ZLIB_DIR)trees.o $(ZLIB_DIR)inftrees.o $(ZLIB_DIR)uncompr.o $(ZLIB_DIR)uncompr.o $(ZLIB_DIR)zutil.o 
   SHARED :=   -lm -lpthread -lc
   CFLAGS += -DSDL_BYTEORDER=SDL_BIG_ENDIAN -DMSB_FIRST -DBYTE_ORDER=BIG_ENDIAN  -DBYTE_ORDER=BIG_ENDIAN \
	-D__CELLOS_LV2 -DPS3PORT=1 -DHAVE_MEMALIGN -DHAVE_ASPRINTF -I$(ZLIB_DIR) 
   PLATFLAGS :=  -DRETRO -DALIGN_DWORD 
else
   CC = i586-mingw32msvc-gcc
#x86_64-w64-mingw32-gcc
   PLATFLAGS :=  -DRETRO -DLSB_FIRST -DALIGN_DWORD -DWIN32PORT -DWIN32
   TARGET := retro-hatari.dll
   SHARED := -shared -static-libgcc -s -Wl,--version-script=$(LIBRETRO)/link.T -Wl,--no-undefined 
endif

ifeq ($(DEBUG), 1)
   CFLAGS += -O0 -g
else
   CFLAGS += -O3
endif

CFLAGS += $(SHARED) $(fpic) \
	-std=gnu99  -finline-functions -funroll-loops  -fsigned-char  \
	-Wno-strict-prototypes -ffast-math -fomit-frame-pointer -fno-strength-reduce  -fno-builtin -finline-functions -s

CXXFLAGS  +=	$(CFLAGS) -std=gnu++0x
CPPFLAGS += $(CFLAGS)

all: $(TARGET)

ifeq ($(platform), wii)
$(TARGET): $(OBJECTS)
	$(AR) rcs $@ $(OBJECTS)
else ifeq ($(platform), ps3)
$(TARGET): $(OBJECTS)
	$(AR) rcs $@ $(OBJECTS)
else ifeq ($(platform), win)
$(TARGET): $(OBJECTS)
	$(CC) $(fpic) $(SHARED) $(INCLUDES) -o $@ $(OBJECTS)  -lm -lz
else
$(TARGET): $(OBJECTS)
	$(CC) $(fpic)  $(SHARED) $(INCLUDES) -o $@ $(OBJECTS)  -lm  

endif

%.o: %.c
	$(CC) $(CFLAGS) $(PLATFLAGS) $(HINCLUDES) -c -o $@ $<

clean:
	rm -f $(OBJECTS) $(TARGET)

.PHONY: clean

