#
# Build Makefile for GNU Make
#

# Notes:
#  As of 6 July 2005, the engine should handle optimisations being enabled.
#  If things seem to be going wrong, lower or disable optimisations, then
#  try again. If things are still going wrong, call me.
#   

include ../Makefile.common

# SDK locations for Windows - adjust to match your setup
#
ifneq ($(DXROOT_OVERRIDE),)
    DXROOT=$(DXROOT_OVERRIDE)
else
    DXROOT=c:/MinGW/sdks/dx80_mgw
#    DXROOT=../../sdk/dx
endif

# Build locations - OBJ gets overridden to the game-specific objects dir
#
OBJ?=obj.gnu
SRC=src
RSRC=rsrc
INC=include

# ENETROOT=$(SRC)/enet

# Filename extensions - these won't need to change
#
o=o
res=o
asm=nasm

# Debugging and release
#
ifneq ($(RELEASE),0)
  # Debugging disabled
  debug=-fomit-frame-pointer -funswitch-loops -O$(OPTLEVEL)
else
  # Debugging enabled
  debug=-ggdb -O0 -DDEBUGGINGAIDS -DNOSDLPARACHUTE
endif

OURCFLAGS=$(BASECFLAGS) -DKSFORBUILD -Wno-char-subscripts -I$(INC)
OURCONLYFLAGS=$(BASECONLYFLAGS)
OURCXXFLAGS=$(BASECXXFLAGS)
OURASFLAGS=$(BASEASFLAGS)
OURLDFLAGS=$(BASELDFLAGS) $(BUILDLDFLAGS)

JAUDIOLIBDIR=$(SRC)/jaudiolib
JAUDIOLIB=libjfaudiolib.a

include Makefile.shared

ENGINEOBJS=
ifeq (0,$(NOASM))
  ENGINEOBJS+= $(OBJ)/a.$o
endif

ENGINEOBJS+= \
	$(OBJ)/a-c.$o \
	$(OBJ)/baselayer.$o \
	$(OBJ)/cache1d.$o \
	$(OBJ)/compat.$o \
	$(OBJ)/crc32.$o \
	$(OBJ)/defs.$o \
	$(OBJ)/engine.$o \
	$(OBJ)/polymost.$o \
	$(OBJ)/hightile.$o \
	$(OBJ)/mdsprite.$o \
	$(OBJ)/textfont.$o \
	$(OBJ)/smalltextfont.$o \
	$(OBJ)/glbuild.$o \
	$(OBJ)/kplib.$o \
	$(OBJ)/lzf_c.$o \
	$(OBJ)/lzf_d.$o \
	$(OBJ)/md4.$o \
	$(OBJ)/osd.$o \
	$(OBJ)/pragmas.$o \
	$(OBJ)/scriptfile.$o \
	$(OBJ)/mutex.$o

ifeq (1,$(POLYMER))
	ENGINEOBJS+=  $(OBJ)/polymer.$o
endif

ifneq (0,$(RANCID_NETWORKING))
	ENGINEOBJS+= $(OBJ)/mmulti_unstable.$o
else
	ENGINEOBJS+= $(OBJ)/mmulti.$o
endif

EDITOROBJS=$(OBJ)/build.$o \
	$(OBJ)/config.$o

# PLATFORM SPECIFIC SETTINGS

ifeq ($(PLATFORM),LINUX)
	OURASFLAGS+= -f elf
endif
ifeq ($(PLATFORM),DARWIN)
	ENGINEOBJS	+= $(OBJ)/StartupWinController.editor.$o \
			$(OBJ)//osxbits.$o
	OURASFLAGS		+= -f Mach-O
	OURCFLAGS	+= -fno-pic
	TARGETOPTS	+= -DNOASM
endif
ifeq ($(PLATFORM),BSD)
	OURASFLAGS+= -f elf
	OURCFLAGS+= -I/usr/X11R6/include
endif
ifeq ($(PLATFORM),WINDOWS)
	OURCFLAGS+= -DUNDERSCORES -I$(DXROOT)/include -I$(FMODROOTWIN)/inc # -I$(ENETROOT)/include
	OURASFLAGS+= -DUNDERSCORES -f win32
endif
ifeq ($(PLATFORM),BEOS)
	OURASFLAGS+= -f elf
	TARGETOPTS+= -DNOASM
endif
ifeq ($(PLATFORM),SYLLABLE)
	OURASFLAGS+= -f elf
endif

ifeq ($(RENDERTYPE),SDL)
	ENGINEOBJS+= $(OBJ)/sdlayer.$o
	OURCFLAGS+= $(subst -Dmain=SDL_main,,$(SDLCONFIG_CFLAGS))
	
	ifeq (1,$(HAVE_GTK2))
		OURCFLAGS+= -DHAVE_GTK2 $(shell pkg-config --cflags gtk+-2.0)
		ENGINEOBJS+= $(OBJ)/gtkbits.$o
		ifeq ($(LINKED_GTK),0)
			ENGINEOBJS+= $(OBJ)/dynamicgtk.$o
		endif
		EDITOROBJS+= $(OBJ)/startgtk.editor.$o
	endif
endif
ifeq ($(RENDERTYPE),WIN)
	ENGINEOBJS+= $(OBJ)/winlayer.$o
	EDITOROBJS+= $(OBJ)/startwin.editor.$o
endif

OURCFLAGS+= $(BUILDCFLAGS)

.SILENT:
.PHONY: clean veryclean all utils enginelib editorlib

# TARGETS
all:
	-echo "Invoking make here does nothing. To build the Build Tools, use a current EDuke32 version."

enginelib: $(OBJ)/$(ENGINELIB)
$(OBJ)/$(ENGINELIB): $(ENGINEOBJS)
	$(BUILD_ECHOFLAGS)
	$(ARCHIVE_STATUS)
	$(AR) rc $@ $^
	$(RANLIB) $@

editorlib: $(OBJ)/$(EDITORLIB)
$(OBJ)/$(EDITORLIB): $(EDITOROBJS)
	$(BUILD_ECHOFLAGS)
	$(ARCHIVE_STATUS)
	$(AR) rc $@ $^
	$(RANLIB) $@

# DEPENDENCIES
include Makefile.deps

# RULES
$(OBJ)/%.$o: $(SRC)/%.nasm
	$(COMPILE_STATUS)
	if $(AS) $(OURASFLAGS) $< -o $@; then $(COMPILE_OK); else $(COMPILE_FAILED); fi

$(OBJ)/%.$o: $(SRC)/%.c
	$(COMPILE_STATUS)
	if $(CC) $(CFLAGS) $(OURCFLAGS) -c $< -o $@; then $(COMPILE_OK); else $(COMPILE_FAILED); fi

$(OBJ)/%.$o: $(SRC)/%.m
	$(COMPILE_STATUS)
	if $(CC) $(CFLAGS) $(OURCFLAGS) -c $< -o $@; then $(COMPILE_OK); else $(COMPILE_FAILED); fi

$(OBJ)/%.$o: $(SRC)/%.cpp
	$(COMPILE_STATUS)
	if $(CXX) $(CXXFLAGS) $(OURCXXFLAGS) $(OURCFLAGS) -c $< -o $@; then $(COMPILE_OK); else $(COMPILE_FAILED); fi

$(OBJ)/%.$o: $(SRC)/%.cxx
	$(CXX) $(CXXFLAGS) $(OURCXXFLAGS) $(OURCFLAGS) -c $< -o $@

$(OBJ)/%.$o: $(SRC)/misc/%.rc
	$(COMPILE_STATUS)
	if $(RC) -i $< -o $@ --include-dir=$(INC) --include-dir=$(SRC); then $(COMPILE_OK); else $(COMPILE_FAILED); fi

$(OBJ)/%.$o: $(RSRC)/%.c
	$(COMPILE_STATUS)
	if $(CC) $(CFLAGS) $(OURCFLAGS) -c $< -o $@; then $(COMPILE_OK); else $(COMPILE_FAILED); fi

$(OBJ)/editor_banner.$o: $(RSRC)/editor_banner.c
	echo "#include <gdk-pixbuf/gdk-pixdata.h>" > $@
	gdk-pixbuf-csource --extern --struct --raw --name=startbanner_pixdata $^ | sed 's/load_inc//' >> $@
$(RSRC)/editor_banner.c: $(RSRC)/build.bmp
	echo "#include <gdk-pixbuf/gdk-pixdata.h>" > $@
	gdk-pixbuf-csource --extern --struct --raw --name=startbanner_pixdata $^ | sed 's/load_inc//' >> $@

# PHONIES	

clean:
	-rm -f $(OBJ)/*

veryclean: clean
	-rm -f $(ENGINELIB) $(EDITORLIB) $(UTILS)

.PHONY: fixlineends
fixlineends:
	for a in `find . -type f \( -name '*.c' -o -name '*.h' -o -name 'Makefile*' \) \! -path '*/.svn/*'`; do \
		echo Fixing $$a && tr -d "\015" < $$a > $$a.fix && mv $$a.fix $$a; \
	done

# DO NOT DELETE


