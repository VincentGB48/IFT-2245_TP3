BUILD_DIR=build
CC=gcc
CFLAGS=-g -std=gnu99 -Wall -pedantic -Isrc -I$(BUILD_DIR)
LDFLAGS=-lm
BISON=bison
FLEX=flex

OBJS = tlb.o parse.tab.o vmm.o pm.o pt.o tokens.o common.o

.PHONY: default all clean format client server test

default: all

all: $(BUILD_DIR)/vmm rapport.pdf tests/gen

$(BUILD_DIR)/vmm: $(patsubst %.o, $(BUILD_DIR)/%.o, $(OBJS))
	$(CC)  -o $@ $(patsubst %.o,$(BUILD_DIR)/%.o, $(OBJS)) $(LDFLAGS)

$(BUILD_DIR)/%.o: src/%.c
	@[ -d "$$(dirname "$@")" ] || mkdir -p "$$(dirname "$@")"
	$(CC) $(CFLAGS) -c -o $@ $<

$(BUILD_DIR)/parse.tab.c $(BUILD_DIR)/parse.tab.h: src/parse.y
	@[ -d "$$(dirname "$@")" ] || mkdir -p "$$(dirname "$@")"
	cd $(BUILD_DIR); $(BISON) -d ../src/parse.y

$(BUILD_DIR)/tokens.c: src/tokens.l
	@[ -d "$$(dirname "$@")" ] || mkdir -p "$$(dirname "$@")"
	$(FLEX) -o $@ $<

tests/gen.c:
	$(CC) tests/gen.c -l src -o tests/gen

run: all
	tests/gen
	$(BUILD_DIR)/vmm tests/BACKING_STORE.txt <tests/command1.in

clean:
	$(RM) -r $(BUILD_DIR) *.aux *.log

%.pdf: %.tex
	pdflatex $<

release:
	tar -czv -f tp3.tar.gz --transform 's|^|tp3/|' \
	    src *.tex *.pdf tests GNUmakefile
