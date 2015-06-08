TEMPDIR=/tmp
A=.a
O=.o

COMPILE_PLATFORM=$(shell uname|sed -e s/_.*//|tr '[:upper:]' '[:lower:]')

ifeq ($(COMPILE_PLATFORM),darwin)
  # Apple does some things a little differently...
  COMPILE_ARCH=$(shell uname -p | sed -e s/i.86/x86/)
else
  COMPILE_ARCH=$(shell uname -m | sed -e s/i.86/x86/)
endif

ifndef PLATFORM
PLATFORM=$(COMPILE_PLATFORM)
endif
export PLATFORM

ifndef ARCH
ARCH=$(COMPILE_ARCH)
endif

ifeq ($(PLATFORM),mingw32)
  E=.exe
else
  E=
endif

CC=gcc
LCC_CFLAGS=-O2 -Wall -fno-strict-aliasing -MMD
LDFLAGS=
LD=gcc
AR=ar
ARFLAGS=cru
RANLIB=ranlib
DIFF=diff
RM=rm -f
RMDIR=rmdir
BUILDDIR=build-$(PLATFORM)-$(ARCH)
BD=$(BUILDDIR)/
MAKE=make

ifeq ($(USE_CCACHE),1)
  CC := ccache $(CC)
endif

# Need MACOS_X defined or this won't build.
ifeq ($(PLATFORM),darwin)
  LCC_CFLAGS += -DMACOS_X
endif

ifeq ($(PLATFORM),sunos)
  INSTALL=ginstall
else
  INSTALL=install
endif

all:
	@echo "=== Building Quake 3 Compiler ==="
	@echo "  Directory: $(BD)"
	@echo "  Targets:"
	@echo "    - q3rcc"
	@echo "    - lburg"
	@echo "    - q3cpp"
	@echo "    - q3lcc"
	@echo ""
	@echo "  Platform: $(PLATFORM)"
	@echo "  Utilities:"
	@echo "    - CC: $(CC)"
	@echo "    - LD: $(LD)"
	@echo "  Flags:"
	@echo "    - CFLAGS: $(LCC_CFLAGS)"
	@echo "    - LDFLAGS: $(LDFLAGS)"
	@echo ""
	@$(MAKE) q3rcc lburg q3cpp q3lcc

q3rcc: makedirs $(BD)q3rcc$(E)
lburg: makedirs $(BD)lburg/lburg$(E)
q3cpp: makedirs $(BD)q3cpp$(E)
q3lcc: makedirs $(BD)q3lcc$(E)

makedirs:
	@echo "MKDIR $(BD)"
	@if [ ! -d $(BD) ];then mkdir $(BD);fi
	@echo "MKDIR $(BD)etc"
	@if [ ! -d $(BD)etc ];then mkdir $(BD)etc;fi
	@echo "MKDIR $(BD)rcc"
	@if [ ! -d $(BD)rcc ];then mkdir $(BD)rcc;fi
	@echo "MKDIR $(BD)cpp"
	@if [ ! -d $(BD)cpp ];then mkdir $(BD)cpp;fi
	@echo "MKDIR $(BD)lburg"
	@if [ ! -d $(BD)lburg ];then mkdir $(BD)lburg;fi

# ===== RCC =====
RCCOBJS= \
	$(BD)rcc/alloc$(O) \
	$(BD)rcc/bind$(O) \
	$(BD)rcc/bytecode$(O) \
	$(BD)rcc/dag$(O) \
	$(BD)rcc/dagcheck$(O) \
	$(BD)rcc/decl$(O) \
	$(BD)rcc/enode$(O) \
	$(BD)rcc/error$(O) \
	$(BD)rcc/event$(O) \
	$(BD)rcc/expr$(O) \
	$(BD)rcc/gen$(O) \
	$(BD)rcc/init$(O) \
	$(BD)rcc/inits$(O) \
	$(BD)rcc/input$(O) \
	$(BD)rcc/lex$(O) \
	$(BD)rcc/list$(O) \
	$(BD)rcc/main$(O) \
	$(BD)rcc/null$(O) \
	$(BD)rcc/output$(O) \
	$(BD)rcc/prof$(O) \
	$(BD)rcc/profio$(O) \
	$(BD)rcc/simp$(O) \
	$(BD)rcc/stmt$(O) \
	$(BD)rcc/string$(O) \
	$(BD)rcc/sym$(O) \
	$(BD)rcc/symbolic$(O) \
	$(BD)rcc/trace$(O) \
	$(BD)rcc/tree$(O) \
	$(BD)rcc/types$(O)

$(BD)q3rcc$(E): $(RCCOBJS)
	@echo "LD $@"
	@$(LD) $(LDFLAGS) -o $@ $(RCCOBJS)
	@echo " => q3rcc$(E)"

$(BD)rcc/%$(O): src/%.c
	@echo "CC $@"
	@$(CC) $(LCC_CFLAGS) -c -Isrc -o $@ $<

$(BD)rcc/dagcheck$(O): $(BD)rcc/dagcheck.c
	@echo "CC $@"
	@$(CC) $(LCC_CFLAGS) -Wno-unused -c -Isrc -o $@ $<

$(BD)rcc/dagcheck.c: $(BD)lburg/lburg$(E) src/dagcheck.md
	@echo "LBURG $@"
	@$(BD)lburg/lburg$(E) src/dagcheck.md $@


# ===== LBURG =====
LBURGOBJS= \
	$(BD)lburg/lburg$(O) \
	$(BD)lburg/gram$(O)

$(BD)lburg/lburg$(E): $(LBURGOBJS)
	@echo "LD $@"
	@$(LD) $(LDFLAGS) -o $@ $(LBURGOBJS)
	@echo " => lburg$(E)"

$(BD)lburg/%$(O): lburg/%.c
	@echo "CC $@"
	@$(CC) $(LCC_CFLAGS) -c -Ilburg -o $@ $<


# ===== CPP =====
CPPOBJS= \
	$(BD)cpp/cpp$(O) \
	$(BD)cpp/lex$(O) \
	$(BD)cpp/nlist$(O) \
	$(BD)cpp/tokens$(O) \
	$(BD)cpp/macro$(O) \
	$(BD)cpp/eval$(O) \
	$(BD)cpp/include$(O) \
	$(BD)cpp/hideset$(O) \
	$(BD)cpp/getopt$(O) \
	$(BD)cpp/unix$(O)

$(BD)q3cpp$(E): $(CPPOBJS)
	@echo "LD $@"
	@$(LD) $(LDFLAGS) -o $@ $(CPPOBJS)
	@echo " => q3cpp$(E)"

$(BD)cpp/%$(O): cpp/%.c
	@echo "CC $@"
	@$(CC) $(LCC_CFLAGS) -c -Icpp -o $@ $<


# ===== LCC =====
LCCOBJS= \
	$(BD)etc/lcc$(O) \
	$(BD)etc/bytecode$(O)

$(BD)q3lcc$(E): $(LCCOBJS)
	@echo "LD $@"
	@$(LD) $(LDFLAGS) -o $@ $(LCCOBJS)
	@echo " => q3lcc$(E)"

$(BD)etc/%$(O): etc/%.c
	@echo "CC $@"
	@$(CC) $(LCC_CFLAGS) -DTEMPDIR=\"$(TEMPDIR)\" -DSYSTEM=\"\" -c -Isrc -o $@ $<


install:
	@echo "=== Installing LCC ==="
	@echo "  Directory: $(BD)"
	@echo "  Targets:"
	@echo "    - q3rcc"
	@echo "    - lburg"
	@echo "    - q3cpp"
	@echo "    - q3lcc"
	@echo ""
	@echo "  Platform: $(PLATFORM)"
	@echo "  Utilities:"
	@echo "    - CC: $(CC)"
	@echo "    - LD: $(LD)"
	@echo "    - INSTALL: $(INSTALL)"
	@echo "  Flags:"
	@echo "    - CFLAGS: $(LCC_CFLAGS)"
	@echo "    - LDFLAGS: $(LDFLAGS)"
	@echo ""
	@$(MAKE) q3lcc q3cpp q3rcc
	@echo ""
	@echo "INSTALL $(BD)q3lcc$(E)"
	@$(INSTALL) -s -m 0755 $(BD)q3lcc$(E) ../
	@echo "INSTALL $(BD)q3cpp$(E)"
	@$(INSTALL) -s -m 0755 $(BD)q3cpp$(E) ../
	@echo "INSTALL $(BD)q3rcc$(E)"
	@$(INSTALL) -s -m 0755 $(BD)q3rcc$(E) ../

uninstall:
	@echo "RM ../q3lcc$(E)"
	@-@$(RM) ../q3lcc$(E)
	@echo "RM ../q3cpp$(E)"
	@-$(RM) ../q3cpp$(E)
	@echo "RM ../q3rcc$(E)"
	@-$(RM) ../q3rcc$(E)

clean:
	@echo "=== CLEANING ==="
	@-find $(BD) -name '*.d' -exec echo "RM {}" \;
	@-if [ -d $(BD) ];then (find $(BD) -name '*.d' -exec rm {} \;)fi
	@for i in $(RCCOBJS); \
	do \
		echo "RM $$i"; \
	done
	@for i in $(LBURGOBJS); \
	do \
		echo "RM $$i"; \
	done
	@for i in $(CPPOBJS); \
	do \
		echo "RM $$i"; \
	done
	@for i in $(LCCOBJS); \
	do \
		echo "RM $$i"; \
	done
	@$(RM) $(RCCOBJS) $(LBURGOBJS) $(CPPOBJS) $(LCCOBJS)
	@echo "RM $(BD)rcc/dagcheck.c"
	@echo "RM $(BD)lburg/lburg$(E)"
	@$(RM) $(BD)rcc/dagcheck.c $(BD)lburg/lburg$(E)
	@echo "RM $(BD)q3lcc$(E)"
	@echo "RM $(BD)q3cpp$(E)"
	@echo "RM $(BD)q3rcc$(E)"
	@$(RM) $(BD)q3lcc$(E) $(BD)q3cpp$(E) $(BD)q3rcc$(E)
	@echo "RM $(BD)"
	@$(RM) -r $(BD)
	@echo "=== CLEAN ==="

D_FILES=$(shell find . -name '*.d')

ifneq ($(strip $(D_FILES)),)
  include $(D_FILES)
endif
