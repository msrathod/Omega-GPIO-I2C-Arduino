# These are the only things to change in this Makefile
# >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
# You NEED to and MUST change TOOL_BIN_DIR to the "bin" directory of your OpenWrt uClibc toolchain
TOOL_BIN_DIR=/home/kit/OmegaCross/OpenWrt-Toolchain-ar71xx-generic_gcc-4.8-linaro_uClibc-0.9.33.2.Linux-x86_64/toolchain-mips_34kc_gcc-4.8-linaro_uClibc-0.9.33.2/bin
# You MAY need to change LIBNEW-GPIO_DIR to relative directory of libnewgpio if you are not using the sources as originally supplied
LIBNEW-GPIO_DIR=../libnewgpio
# You MAY need to change LIBNEW-I2C_DIR to relative directory of libnewi2c if you are not using the sources as originally supplied
LIBNEW-I2C_DIR=../libnewi2c
# You MAY need to change LIBNEW-I2C_DIR to relative directory of libarduino if you are not using the sources as originally supplied
LIBARDUINO_DIR=../libarduino
# Change PROGNAME to the name you want the built program to have
PROGNAME=iotemplate
# >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

SHELL := /bin/bash
# Use buildep=1 on make command line to also build libnewgpio, libnewi2c, libarduino first
builddep?=0

# C++ compiler
CC=$(TOOL_BIN_DIR)/mips-openwrt-linux-uclibc-g++
CCFLAGS=-c -Os -s -fPIC -std=gnu++11

# Source files
SOURCES=$(shell find ./src/ -name "*.cpp" | sed s:./src/:: | tr "\n" " ")
OBJECTS=$(patsubst %.cpp,build/%.o,$(SOURCES))

# Includes and libraries
INC=-I$(LIBNEW-GPIO_DIR)/hdr -I$(LIBNEW-I2C_DIR)/hdr -I$(LIBARDUINO_DIR)/hdr -I./hdr
LIBS_A=$(LIBNEW-GPIO_DIR)/bin/static/libnewgpio.a $(LIBNEW-I2C_DIR)/bin/static/libnewi2c.a $(LIBARDUINO_DIR)/bin/static/libarduino.a
LIBS_SO=$(LIBNEW-GPIO_DIR)/bin/dynamic/libnewgpio.so $(LIBNEW-I2C_DIR)/bin/dynamic/libnewi2c.so $(LIBARDUINO_DIR)/bin/dynamic/libarduino.so
SYS_LIBS=-lpthread -ldl

# Main build target
TARGET_A=bin/static/$(PROGNAME)
TARGET_SO=bin/dynamic/$(PROGNAME)

# Default target - build everything
all: static dynamic

# The main static link build 
ifeq ($(builddep),1)
static: precomp MAKE_LIB $(TARGET_A)
else
static: precomp $(TARGET_A)
endif

# The main dynamic link build 
ifeq ($(builddep),1)
dynamic: precomp MAKE_LIB $(TARGET_SO)
else
dynamic: precomp $(TARGET_SO)
endif

# Ensure library is built
MAKE_LIB:
	pushd $(LIBNEW-GPIO_DIR); make clean; make builddep=$(builddep); popd
	pushd $(LIBNEW-I2C_DIR); make clean; make builddep=$(builddep); popd
	pushd $(LIBARDUINO_DIR); make clean; make builddep=$(builddep); popd

# Link static
$(TARGET_A): $(OBJECTS)
	$(CC) -o $(TARGET_A) $(OBJECTS) $(LIBS_A) $(SYS_LIBS)

# Link dynamic
$(TARGET_SO): $(OBJECTS)
	$(CC) -o $(TARGET_SO) $(OBJECTS) $(LIBS_SO) $(SYS_LIBS)

# Compile sources 
build/%.o: ./src/%.cpp
	$(CC) $(CCFLAGS) $(INC) $< -o $@

# Remove all created files
clean-static:
	rm -rf bin/static

clean-dynamic:
	rm -rf bin/dynamic

clean: clean-static clean-dynamic
	rm -rf build/*
	rm -rf bin/*

# Ensure required sub-directories are created
precomp:
	@mkdir -p build
	@mkdir -p bin
	@mkdir -p bin/static
	@mkdir -p bin/dynamic
