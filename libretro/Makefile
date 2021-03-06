LIBRETRO_EMBED_FFMPEG        = 0

INTERNAL_LIBAVCODEC    = 0
INTERNAL_LIBAVFORMAT   = 0
INTERNAL_LIBAVUTIL     = 0
INTERNAL_LIBSWRESAMPLE = 0
INTERNAL_LIBSWSCALE    = 0

INTERNAL_LIBFLAC       = 0
INTERNAL_LIBOGG        = 0
INTERNAL_LIBLAME       = 0
INTERNAL_LIBFAAC       = 0
INTERNAL_LIBSPEEX      = 0
INTERNAL_LIBTHEORA     = 0
INTERNAL_LIBOPUS       = 0
INTERNAL_LIBVORBIS     = 0
INTERNAL_WEBP          = 0
INTERNAL_ZLIB          = 0
INTERNAL_WAVPACK       = 0
INTERNAL_XVIDCORE      = 0
INTERNAL_VPX           = 0
STATIC_LINKING         = 0

WANT_LIBASS            = 0

GLFLAGS   :=

BAKE_IN_FFMPEG := 0

#CPU Optimization flags
HAVE_SSE2 = 0
HAVE_MMX = 0
HAVE_THREADS=1

#Additional codec support for internal FFmpeg
HAVE_WAVPACK   = 0
HAVE_LIBSPEEX  = 0
HAVE_LIBWEBP   = 0
HAVE_LIBOGG    = 0
HAVE_LIBX264   = 0
HAVE_LIBX265   = 0
HAVE_LIBFAAC   = 0
HAVE_LIBFLAC   = 0
HAVE_LIBLAME   = 0
HAVE_LIBTWOLAME = 0
HAVE_LIBVORBIS = 0
HAVE_LIBTHEORA = 0
HAVE_LIBOPUS   = 0
HAVE_ZLIB      = 0

#Video support
OPENGL 		  = 1
HAVE_CODEC_HW = 0

CORE_DIR := .

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

system = $(shell uname -m)

ifeq ($(system),x86_64)
ARCH_X86 = 1
ARCH_X86_64 = 1
endif

TARGET_NAME := ffmpeg

GIT_VERSION ?= " $(shell git rev-parse --short HEAD || echo unknown)"
ifneq ($(GIT_VERSION)," unknown")
       CFLAGS += -DGIT_VERSION=\"$(GIT_VERSION)\"
endif

ifneq (,$(findstring unix,$(platform)))
	ARCH_X86 = 1
   TARGET := $(TARGET_NAME)_libretro.so
   fpic := -fPIC
   SHARED := -shared -Wl,--version-script=link.T -Wl,--no-undefined -fPIC
ifeq ($(OPENGL),1)
   GL_LIB := -lGL
	HAVE_OPENGL = 1
	HAVE_GL_FFT := 1
endif
   HAVE_SSA := 1

	HAVE_POLL_H = 1
	HAVE_GETADDRINFO = 1
	HAVE_NETWORK = 1
	HAVE_SOCKLEN = 1
	HAVE_PTHREADS=1

else ifneq (,$(findstring osx,$(platform)))

   ARCH_X86 = 1
   LIBRETRO_EMBED_FFMPEG = 1
   TARGET := $(TARGET_NAME)_libretro.dylib
   fpic := -fPIC
   SHARED := -dynamiclib
   CFLAGS += -I. -I.. -DHAVE_FMINF

ifeq ($(OPENGL),1)
   GL_LIB := -framework OpenGL
   HAVE_GL_FFT := 1
	HAVE_OPENGL = 1
endif
   OSXVER = `sw_vers -productVersion | cut -d. -f 2`
   OSX_LT_MAVERICKS = `(( $(OSXVER) <= 9)) && echo "YES"`
   fpic += -mmacosx-version-min=10.1
	HAVE_POLL_H = 1
	HAVE_GETADDRINFO = 1
	HAVE_NETWORK = 1
	HAVE_SOCKLEN = 1
	HAVE_PTHREADS=1

# Nintendo Switch (libnx)
else ifeq ($(platform), libnx)
    include $(DEVKITPRO)/libnx/switch_rules
    EXT=a
    TARGET := $(TARGET_NAME)_libretro_$(platform).$(EXT)
    DEFINES := -DSWITCH=1 -D__SWITCH__=1 -DHAVE_LIBNX=1 
    CFLAGS	:=	 $(DEFINES) -g -fPIE -I$(LIBNX)/include/ -I$(PORTLIBS)/include/ -ffunction-sections -fdata-sections -ftls-model=local-exec
    CFLAGS += $(INCDIRS) -I$(CORE_DIR)/zlib -I$(CORE_DIR)/libogg/include -I$(CORE_DIR)/libvorbis/include \
		-I$(CORE_DIR)/jpeg-8c -fvisibility=hidden
    CFLAGS	+= -march=armv8-a -mtune=cortex-a57 -mtp=soft -mcpu=cortex-a57+crc+fp+simd
    CFLAGS += -DHAVE_STDINT_H=1 -DHAVE_INT32_T=1 -DHAVE_STRUCT_ADDRINFO=1 -DHAVE_STRUCT_SOCKADDR_STORAGE=1 -DHAVE_ARPA_INET_H=1 -DCONFIG_NETWORK=1 -DHAVE_SOCKLEN_T=1 -DCONFIG_FILE_PROTOCOL=1
    CXXFLAGS := $(ASFLAGS) $(CFLAGS)
    STATIC_LINKING = 1
    HAVE_POLL_H = 1
	BAKE_IN_FFMPEG := 0
	LIBRETRO_EMBED_FFMPEG := 0
	ARCH_X86 = 0
	HAVE_GETADDRINFO := 1
# CTR (3DS)
else ifeq ($(platform), ctr)
	TARGET := $(TARGET_NAME)_libretro_$(platform).a
	CC = $(DEVKITARM)/bin/arm-none-eabi-gcc$(EXE_EXT)
	CXX = $(DEVKITARM)/bin/arm-none-eabi-g++$(EXE_EXT)
	AR = $(DEVKITARM)/bin/arm-none-eabi-ar$(EXE_EXT)
	CFLAGS += -D_3DS -DARM11 -march=armv6k -mtune=mpcore -mfloat-abi=hard -I$(DEVKITPRO)/libctru/include -DHAVE_STDINT_H=1 -DHAVE_INT32_T=1 -DHAVE_STRUCT_ADDRINFO=1 -DHAVE_STRUCT_SOCKADDR_STORAGE=1 -include stdint.h -include libretro-common/rthreads/ctr_pthread.h -DHAVE_ARPA_INET_H=1 -DCONFIG_NETWORK=1 -DHAVE_SOCKLEN_T=1 -Dstrtoumax=strtoull -DCONFIG_FILE_PROTOCOL=1
	HAVE_POLL_H = 1
	CXXFLAGS += -D_3DS -DARM11 -march=armv6k -mtune=mpcore -mfloat-abi=hard -I$(DEVKITPRO)/libctru/include -DHAVE_STDINT_H=1 -DHAVE_INT32_T=1 -DHAVE_STRUCT_ADDRINFO=1 -DHAVE_STRUCT_SOCKADDR_STORAGE=1 -DHAVE_ARPA_INET_H=1 -DCONFIG_NETWORK=1 -DHAVE_SOCKLEN_T=1 -Dstrtoumax=strtoull -DCONFIG_FILE_PROTOCOL=1
	STATIC_LINKING = 1
	BAKE_IN_FFMPEG := 1
	LIBRETRO_EMBED_FFMPEG := 1
	ARCH_X86 = 0
	HAVE_GETADDRINFO := 1
else ifneq (,$(findstring ios,$(platform)))

   #ARCH_ARM = 1
   LIBRETRO_EMBED_FFMPEG = 1
   TARGET := $(TARGET_NAME)_libretro_ios.dylib
   fpic := -fPIC
   SHARED := -dynamiclib
   CFLAGS += -I. -I.. -DHAVE_FMINF -DIOS -DHAVE_UNISTD_H
ifeq ($(OPENGL),1)
	HAVE_OPENGL = 1
	GLES = 1
   GL_LIB := -framework OpenGLES
   HAVE_GL_FFT := 1
   CFLAGS += -DHAVE_OPENGLES -DHAVE_OPENGLES3
endif
   HAVE_ARMV7=1
   HAVE_PTHREADS=1
   ARCH_X86 = 0
   ARCH_X86_64 = 0

ifeq ($(IOSSDK),)
  IOSSDK := $(shell xcodebuild -version -sdk iphoneos Path)
endif

ifeq ($(platform),ios-arm64)
   CC = cc -arch arm64 -isysroot $(IOSSDK)
   CXX  = c++ -arch arm64 -isysroot $(IOSSDK)
else
   CC = cc -arch armv7 -isysroot $(IOSSDK)
   CXX  = c++ -arch armv7 -isysroot $(IOSSDK)
   LD = armv7-apple-darwin11-ld
endif

ifeq ($(platform),$(filter $(platform),ios9 ios-arm64))
   CC += -miphoneos-version-min=8.0
   CFLAGS += -miphoneos-version-min=8.0
else
   CC += -miphoneos-version-min=5.0
   CFLAGS += -miphoneos-version-min=5.0
endif
	CFLAGS += -DHAVE_STRUCT_SOCKADDR_STORAGE -DHAVE_STRUCT_ADDRINFO
	HAVE_POLL_H = 1
	HAVE_GETADDRINFO = 1
	HAVE_NETWORK = 1
	HAVE_SOCKLEN = 1

else ifeq ($(platform), emscripten)
	ARCH_X86 = 0
	ARCH_X86_64=0
	ARCH_ARM = 0
	TARGET := $(TARGET_NAME)_libretro_$(platform).bc
	STATIC_LINKING=1
   HAVE_PTHREADS=1
   LIBRETRO_EMBED_FFMPEG = 1
	CFLAGS += -DHAVE_UNISTD_H
	CFLAGS += -DHAVE_STRUCT_SOCKADDR_STORAGE -DHAVE_STRUCT_ADDRINFO
	HAVE_POLL_H = 1
	HAVE_GETADDRINFO = 1
	HAVE_NETWORK = 1
	HAVE_SOCKLEN = 1
	HAVE_PTHREADS=1

# Windows
else

	ARCH_X86 = 1
   TARGET := $(TARGET_NAME)_libretro.dll
   SHARED := -shared -static-libgcc -static-libstdc++ -s -Wl,--version-script=link.T -Wl,--no-undefined
   CFLAGS += -Iffmpeg
ifeq ($(OPENGL),1)
   GL_LIB := -lopengl32
   CFLAGS += -DHAVE_OPENGL
	HAVE_OPENGL = 1
	HAVE_GL_FFT := 1
	WIN32_PLATFORM := 1
endif
   LIBS += -L.
endif

ifeq ($(LIBRETRO_EMBED_FFMPEG), 1)
   CFLAGS += -DUPSTREAM_VERSION=\"$(shell cat ../RELEASE)\"
endif

CFLAGS += -D__LIBRETRO__

include Makefile.common

CFLAGS += $(DEFINES) $(INCFLAGS) $(GLFLAGS) $(DEF_FLAGS)
CFLAGS += -Wall $(fpic)

ifeq ($(DEBUG), 1)
   CFLAGS += -O0 -g
else
   CFLAGS += -O3
endif

OBJECTS := $(SOURCES_C:.c=.o) $(SOURCES_CXX:.cpp=.o)

all: $(TARGET)

%.o: %.c
	$(CC) -c -std=gnu99 -o $@ $< $(CFLAGS)

%.o: %.cpp
	$(CXX) -c -fno-strict-aliasing -o $@ $< $(CFLAGS)

$(TARGET): $(OBJECTS)
ifeq ($(STATIC_LINKING),1)
	$(AR) rcs $@ $(OBJECTS)
else
	$(CXX) -o $@ $^ $(LIBS) $(SHARED)
endif

clean:
	rm -f $(OBJECTS)
	rm -f $(TARGET)

.PHONY: clean
