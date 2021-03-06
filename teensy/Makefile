# The name of your project (used to name the compiled .hex file)
TARGET = $(notdir $(CURDIR))

# The teensy version to use, 30, 31, 35, 36, or LC
TEENSY = 36

# Set to 24000000, 48000000, or 96000000 to set CPU core speed
#TEENSY_CORE_SPEED = 180000000
#TEENSY_CORE_SPEED = 216000000
#TEENSY_CORE_SPEED = 240000000	# Best!!
TEENSY_CORE_SPEED = 256000000

# Some libraries will require this to be defined
# If you define this, you will break the default main.cpp
#ARDUINO = 10805

# configurable options
#OPTIONS = -DUSB_SERIAL -DLAYOUT_US_ENGLISH
OPTIONS = -DUSB_AUDIO -DLAYOUT_US_ENGLISH

# directory to build in
#BUILDDIR = $(abspath $(CURDIR)/build)
BUILDDIR = /tmp/build

#************************************************************************
# Location of Teensyduino utilities, Toolchain, and Arduino Libraries.
# To use this makefile without Arduino, copy the resources from these
# locations and edit the pathnames.  The rest of Arduino is not needed.
#************************************************************************

# path location for Teensy Loader, teensy_post_compile and teensy_reboot
TOOLSPATH = $(CURDIR)/tools

ifeq ($(OS),Windows_NT)
    $(error What is Win Dose?)
else
    UNAME_S := $(shell uname -s)
    ifeq ($(UNAME_S),Darwin)
        TOOLSPATH = /Applications/Arduino.app/Contents/Java/hardware/tools/
    endif
endif

# path location for Teensy 3 core
COREPATH = teensy3

# path location for Arduino libraries
LIBRARYPATH = libraries

# path location for the arm-none-eabi compiler
COMPILERPATH = $(TOOLSPATH)/arm/bin
#COMPILERPATH = $(TOOLSPATH)/armclang/bin

#************************************************************************
# Settings below this point usually do not need to be edited
#************************************************************************

# CPPFLAGS = compiler options for C and C++
#CPPFLAGS = -Wall -g -Os -mthumb -ffunction-sections -fdata-sections -nostdlib -MMD $(OPTIONS) -DTEENSYDUINO=124 -DF_CPU=$(TEENSY_CORE_SPEED) -Isrc -I$(COREPATH)
CPPFLAGS = -Wall -Ofast -fomit-frame-pointer -fmodulo-sched -fmodulo-sched-allow-regmoves -flto -finline-functions -funswitch-loops -fpredictive-commoning -fgcse-after-reload -ftree-vectorize -fipa-cp-clone -fgraphite-identity -floop-block -floop-interchange -floop-strip-mine -ftree-loop-distribution -ftree-loop-linear -mthumb -ffunction-sections -fdata-sections -nostdlib -MMD $(OPTIONS) -DTEENSYDUINO=144 -DF_CPU=$(TEENSY_CORE_SPEED) -Isrc -I$(COREPATH)

# compiler options for C++ only
CXXFLAGS = -std=gnu++14 -felide-constructors -fno-exceptions -fno-rtti

# compiler options for C only
CFLAGS =

# linker options
LDFLAGS = -s -Wl,--gc-sections,--defsym=__rtc_localtime=0 --specs=nano.specs -mthumb

# additional libraries to link
LIBS = -lm

# compiler options specific to teensy version
ifeq ($(TEENSY), 30)
    CPPFLAGS += -D__MK20DX128__ -mcpu=cortex-m4
    LDSCRIPT = $(COREPATH)/mk20dx128.ld
    LDFLAGS += -mcpu=cortex-m4 -T$(LDSCRIPT)
else ifeq ($(TEENSY), 31)
    CPPFLAGS += -D__MK20DX256__ -mcpu=cortex-m4
    LDSCRIPT = $(COREPATH)/mk20dx256.ld
    LDFLAGS += -mcpu=cortex-m4 -T$(LDSCRIPT)
else ifeq ($(TEENSY), LC)
    CPPFLAGS += -D__MKL26Z64__ -mcpu=cortex-m0plus
    LDSCRIPT = $(COREPATH)/mkl26z64.ld
    LDFLAGS += -mcpu=cortex-m0plus -T$(LDSCRIPT)
    LIBS += -larm_cortexM0l_math
else ifeq ($(TEENSY), 35)
    CPPFLAGS += -D__MK64FX512__ -mcpu=cortex-m4 -mfloat-abi=hard -mfpu=fpv4-sp-d16
    LDSCRIPT = $(COREPATH)/mk64fx512.ld
    LDFLAGS += -mcpu=cortex-m4 -mfloat-abi=hard -mfpu=fpv4-sp-d16 -T$(LDSCRIPT)
    LIBS += -larm_cortexM4lf_math
else ifeq ($(TEENSY), 36)
    CPPFLAGS += -D__MK66FX1M0__ -mcpu=cortex-m4 -mfloat-abi=hard -mfpu=fpv4-sp-d16 -mtune=cortex-m4
    LDSCRIPT = $(COREPATH)/mk66fx1m0.ld
    LDFLAGS += -mcpu=cortex-m4 -mfloat-abi=hard -mfpu=fpv4-sp-d16 -T$(LDSCRIPT)
    LIBS += -larm_cortexM4lf_math
    #LIBS += -larm_cortexM4l_math
else
    $(error Invalid setting for TEENSY)
endif

# set arduino define if given
ifdef ARDUINO
	CPPFLAGS += -DARDUINO=$(ARDUINO)
else
	CPPFLAGS += -DUSING_MAKEFILE
endif

# names for the compiler programs
#CLANG = 1
ifdef CLANG
	export ARMLMD_LICENSE_FILE = $(abspath $(TOOLSPATH))/armclang/license.txt
	ARM_PRODUCT_PATH = $(abspath $(TOOLSPATH))/sw/mappings
	CC = $(abspath $(COMPILERPATH))/armclang
	CXX = $(abspath $(COMPILERPATH))/armclang
	OBJCOPY = $(abspath $(COMPILERPATH))/arm-none-eabi-objcopy
	SIZE = $(abspath $(COMPILERPATH))/arm-none-eabi-size
else
	AS = $(abspath $(COMPILERPATH))/arm-none-eabi-as
	CC = $(abspath $(COMPILERPATH))/arm-none-eabi-gcc
	CXX = $(abspath $(COMPILERPATH))/arm-none-eabi-g++
	OBJCOPY = $(abspath $(COMPILERPATH))/arm-none-eabi-objcopy
	SIZE = $(abspath $(COMPILERPATH))/arm-none-eabi-size
endif

# automatically create lists of the sources and objects
LC_FILES := $(wildcard $(LIBRARYPATH)/*/*.c)
LCPP_FILES := $(wildcard $(LIBRARYPATH)/*/*.cpp)
TC_FILES := $(wildcard $(COREPATH)/*.c)
TCPP_FILES := $(wildcard $(COREPATH)/*.cpp)
S_FILES := $(wildcard $(COREPATH)/*.S)
C_FILES := $(wildcard src/*.c)
CPP_FILES := $(wildcard src/*.cpp)
INO_FILES := $(wildcard src/*.ino)

# include paths for libraries
L_INC := $(foreach lib,$(filter %/, $(wildcard $(LIBRARYPATH)/*/)), -I$(lib))

SOURCES := $(C_FILES:.c=.o) $(CPP_FILES:.cpp=.o) $(INO_FILES:.ino=.o) $(TC_FILES:.c=.o) $(TCPP_FILES:.cpp=.o) $(LC_FILES:.c=.o) $(LCPP_FILES:.cpp=.o) $(S_FILES:.S=.o)
OBJS := $(foreach src,$(SOURCES), $(BUILDDIR)/$(src))

all: hex

build: $(TARGET).elf

hex: $(TARGET).hex

blink:
	@$(abspath $(TOOLSPATH))/teensy_loader_cli -mmcu=mk66fx1m0 -w -v teensy36-led.hex

post_compile: $(TARGET).hex
	@$(abspath $(TOOLSPATH))/teensy_loader_cli -mmcu=mk66fx1m0 -w -v "$(basename $<)".hex

reboot:
	@-$(abspath $(TOOLSPATH))/teensy_loader_cli -mmcu=mk66fx1m0 -r ../teensy_loader_cli_src/rebootor/rebootor.hex

#upload: post_compile reboot
upload: post_compile

$(BUILDDIR)/%.o: %.S
	@echo -e "[AS]\t$<"
	@mkdir -p "$(dir $@)"
	@$(CC) $(SFLAGS) $(CPPFLAGS) $(CFLAGS) $(L_INC) -o "$@" -c "$<"

$(BUILDDIR)/%.o: %.c
	@echo -e "[CC]\t$<"
	@mkdir -p "$(dir $@)"
	@$(CC) $(CPPFLAGS) $(CFLAGS) $(L_INC) -o "$@" -c "$<"

$(BUILDDIR)/%.o: %.cpp
	@echo -e "[CXX]\t$<"
	@mkdir -p "$(dir $@)"
	@$(CXX) $(CPPFLAGS) $(CXXFLAGS) $(L_INC) -o "$@" -c "$<"

$(BUILDDIR)/%.o: %.ino
	@echo -e "[CXX]\t$<"
	@mkdir -p "$(dir $@)"
	@$(CXX) $(CPPFLAGS) $(CXXFLAGS) $(L_INC) -o "$@" -x c++ -include Arduino.h -c "$<"

$(TARGET).elf: $(OBJS) $(LDSCRIPT)
	@echo -e "[LD]\t$@"
	@$(CC) $(LDFLAGS) -o "$@" $(OBJS) $(LIBS)

%.hex: %.elf
	@echo -e "[HEX]\t$@"
	@$(SIZE) "$<"
	@$(OBJCOPY) -O ihex -R .eeprom "$<" "$@"

# compiler generated dependency info
-include $(OBJS:.o=.d)

clean:
	@echo Cleaning...
	@rm -rf "$(BUILDDIR)"
	@rm -f "$(TARGET).elf" "$(TARGET).hex"
