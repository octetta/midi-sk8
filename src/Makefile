# defaults for Linux

TARGET = linux
CC = gcc
LIB = -lm -lasound -pthread -lpthread -lrt
COPTS = -D_GNU_SOURCE -Wall -march=native -O3
# COPTS = -D_GNU_SOURCE -Wall -march=native -g # -O3

UNAME_S = $(shell uname -s)

ifeq ($(UNAME_S), Darwin)
	CC = clang
	LIB = -lm -pthread -lpthread -framework CoreAudio -framework CoreFoundation 
	TARGET = darwin
	COPTS = -g -D_GNU_SOURCE -D_IS_OSX_ -Wall -arch arm64  -arch x86_64
	# COPTS = -D_GNU_SOURCE -D_IS_OSX_ -Wall -arch arm64 
	# DOPTS = -Wl,-stack_size,0x800000
endif

OUT = build/$(TARGET)
WINOUT = build/win

all : midi-sk8-$(TARGET)

# Linker Flags
# -s -w strips debug info for smaller binaries
# -H=windowsgui hides the terminal window on Windows launch
# -extldflags '-static...' ensures all C++ dependencies are bundled into the exe
WIN_LDFLAGS="-H=windowsgui -s -w -extldflags '-static -static-libgcc -static-libstdc++'"
LINUX_LDFLAGS="-s -w"

# go install fyne.io/tools/cmd/fyne@latest

GOWINENV = CGO_ENABLED=1 GOOS=windows GOARCH=amd64 CC=x86_64-w64-mingw32-gcc CXX=x86_64-w64-mingw32-g++
GOWINLD = -ldflags="-H=windowsgui -s -w -extldflags '-static -static-libgcc -static-libstdc++'"

setup-midi:
	sudo dnf install mingw64-gcc mingw64-winpthreads-static alsa-lib-devel
	cd midi-sk8 && go mod tidy

midi-sk8-linux:
	mkdir -p $(OUT)
	cd midi-sk8 && go build -o ../$(OUT)/midi-sk8-linux .

midi-sk8-darwin:
	mkdir -p $(OUT)
	cd midi-sk8 && CGO_ENABLED=1 GOOS=darwin GOARCH=amd64 go build -o ../$(OUT)/midi-sk8-darwin-amd64 .
	cd midi-sk8 && CGO_ENABLED=1 GOOS=darwin GOARCH=arm64 go build -o ../$(OUT)/midi-sk8-darwin-arm64 .
	cd midi-sk8 && lipo -create -output ../$(OUT)/midi-sk8-darwin-universal ../$(OUT)/midi-sk8-darwin-amd64 ../$(OUT)/midi-sk8-darwin-arm64
	cd midi-sk8 && fyne package -exe ../$(OUT)/midi-sk8-darwin-universal -os darwin -icon icon.png -name ../$(OUT)/midi-sk8

midi-sk8-windows:
	mkdir -p $(WINOUT)
	cd midi-sk8 && \
    $(GOWINENV) \
    fyne package --release -os windows && \
    cp midi-sk8.exe ../$(WINOUT)

clean :
	rm -rf build
	find . -name '*.exe' -exec rm -f {} \;
