# yeah, couldn't do more simple really

COMPILE_PLATFORM=$(shell uname|sed -e s/_.*//|tr '[:upper:]' '[:lower:]')

ifndef PLATFORM
PLATFORM=$(COMPILE_PLATFORM)
endif
export PLATFORM

ifeq ($(PLATFORM),mingw32)
  BINEXT=.exe
else
  BINEXT=
endif

ifeq ($(PLATFORM),sunos)
  INSTALL=ginstall
else
  INSTALL=install
endif

CC=gcc
Q3ASM_CFLAGS=-O2 -Wall -fno-strict-aliasing
MAKE=make

ifeq ($(PLATFORM),darwin)
  LCC_CFLAGS += -DMACOS_X=1
endif

ifndef USE_CCACHE
  USE_CCACHE=0
endif

ifeq ($(USE_CCACHE),1)
  CC := ccache $(CC)
  CXX := ccache $(CXX)
endif

default:
	@echo "=== Building Quake 3 Assembler ==="
	@echo "  Directory: $(BD)"
	@echo "  Targets:"
	@echo "    - q3asm"
	@echo ""
	@echo "  Platform: $(PLATFORM)"
	@echo "  Utilities:"
	@echo "    - CC: $(CC)"
	@echo "  Flags:"
	@echo "    - CFLAGS: $(LCC_CFLAGS)"
	@echo ""
	@$(MAKE) q3asm$(BINEXT)
	@echo "=== Built ==="

q3asm$(BINEXT): q3asm.c cmdlib.c
	@echo "CC $@"
	@$(CC) $(Q3ASM_CFLAGS) -o $@ $^

clean:
	@echo "=== CLEANING ==="
	@echo "RM q3asm$(BINEXT)"
	@find . -name '*.o' -exec echo "RM {}" \;
	@find . -name '*~' -exec echo "RM {}" \;
	@rm -f q3asm$(BINEXT) *~ *.o
	@echo "=== CLEAN ==="

install:
	@echo "=== Installing Quake 3 Assembler ==="
	@echo "  Directory: $(BD)"
	@echo "  Targets:"
	@echo "    - q3asm"
	@echo ""
	@echo "  Platform: $(PLATFORM)"
	@echo "  Utilities:"
	@echo "    - CC: $(CC)"
	@echo "    - INSTALL: $(INSTALL)"
	@echo "  Flags:"
	@echo "    - CFLAGS: $(LCC_CFLAGS)"
	@echo ""
	@$(MAKE) q3asm$(BINEXT)
	@echo "INSTALL q3asm$(BINEXT)"
	@$(INSTALL) -s -m 0755 q3asm$(BINEXT) ../
	@echo "=== Installed ==="

uninstall:
	@echo "RM ../q3asm$(BINEXT)"
	@rm -f ../q3asm$(BINEXT)
