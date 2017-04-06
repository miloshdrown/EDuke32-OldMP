#
# EDuke32 Makefile for GNU Make
#

include Makefile.common

# Build locations
SRC=source
RSRC=rsrc
EROOT=build
ESRC=$(EROOT)/src
EINC=$(EROOT)/include
INC=$(SRC)
o=o
# ENETROOT=$(ESRC)/enet

JAUDIOLIBDIR=$(SRC)/jaudiolib
JAUDIOLIB=libjfaudiolib.a

LIBS=-lm

include $(EROOT)/Makefile.shared

EDUKE32 ?= eduke32-oldmp$(EXESUFFIX)
MAPSTER32 ?= mapster32$(EXESUFFIX)

EDUKE32_TARGET:=$(EDUKE32)
MAPSTER32_TARGET:=
#$(MAPSTER32)

EBACKTRACEDLL ?= ebacktrace1.dll
EBACKTRACEDLL_TARGET:=


# SDK locations - adjust to match your setup
ifneq ($(DXROOT_OVERRIDE),)
    DXROOT=$(DXROOT_OVERRIDE)
else
    DXROOT=c:/MinGW/sdks/dx80_mgw
#    DXROOT=../sdk/dx
endif

OURCFLAGS=$(BASECFLAGS) \
    -I$(INC) -I$(EINC) -I$(SRC)/jmact -I$(JAUDIOLIBDIR)/include
OURCXXFLAGS=$(BASECXXFLAGS)
OURCONLYFLAGS=$(BASECONLYFLAGS)
OURASFLAGS=$(BASEASFLAGS)
OURLDFLAGS=$(BASELDFLAGS) $(BUILDLDFLAGS)

ifeq ($(PLATFORM),WINDOWS)
    OBJ=obj_win
    EOBJ=eobj_win
    ifeq (0,$(RELEASE))
        EBACKTRACEDLL_TARGET=$(EBACKTRACEDLL)
    endif

    LIBS+= -LWindows/lib
    OURCFLAGS+= -IWindows/include
else
    OBJ=obj
    EOBJ=eobj

    ifeq ($(PLATFORM),DARWIN)
        LIBS+= -L/opt/local/lib -L/usr/local/lib
        OURCFLAGS+= -I/opt/local/include -I/usr/local/include
    endif
endif

JMACTOBJ=$(OBJ)/util_lib.$o \
	$(OBJ)/file_lib.$o \
	$(OBJ)/control.$o \
	$(OBJ)/keyboard.$o \
	$(OBJ)/mouse.$o \
	$(OBJ)/mathutil.$o \
	$(OBJ)/scriplib.$o \
	$(OBJ)/animlib.$o

GAMEOBJS=$(OBJ)/game.$o \
	$(OBJ)/actors.$o \
	$(OBJ)/anim.$o \
	$(OBJ)/config.$o \
	$(OBJ)/gamedef.$o \
	$(OBJ)/gameexec.$o \
	$(OBJ)/gamevars.$o \
	$(OBJ)/global.$o \
	$(OBJ)/menus.$o \
	$(OBJ)/namesdyn.$o \
	$(OBJ)/player.$o \
	$(OBJ)/premap.$o \
	$(OBJ)/savegame.$o \
	$(OBJ)/sector.$o \
	$(OBJ)/rts.$o \
	$(OBJ)/testcd.$o \
	$(OBJ)/osdfuncs.$o \
	$(OBJ)/osdcmds.$o \
	$(OBJ)/grpscan.$o \
	$(OBJ)/sounds.$o \
	$(JMACTOBJ)

EDITOROBJS=$(OBJ)/astub.$o

# PLATFORM SPECIFIC SETTINGS

ifeq ($(PLATFORM),LINUX)
	OURCFLAGS	+= -fno-pic
	OURASFLAGS	+= -f elf
	LIBS		+= -lvorbisfile -lvorbis -logg 
endif

ifeq ($(PLATFORM),DARWIN)
	ifeq (1,$(SDL_FRAMEWORK))
		OURCFLAGS	+= -fno-pic -IApple/include -I/Library/Frameworks/SDL.framework/Headers \
				-I-I/Library/Frameworks/SDL_mixer.framework/Headers
		LIBS		+= -read_only_relocs suppress -LApple/lib -lvorbisfile -lvorbis -logg -lm \
				-Wl,-framework,SDL -Wl,-framework,SDL_mixer Apple/lib/libSDLmain.a \
				-Wl,-framework,Cocoa -Wl,-framework,Carbon -Wl,-framework,OpenGL \
				-Wl,-framework,CoreMidi -Wl,-framework,AudioUnit \
				-Wl,-framework,AudioToolbox -Wl,-framework,IOKit -Wl,-framework,AGL \
				-Wl,-framework,QuickTime -lm

	else
		OURCFLAGS	+= -fno-pic -I$(SDLROOT)/include -I$(SDLROOT)/include/SDL
		LIBS		+= -read_only_relocs suppress -lvorbisfile -lvorbis -logg -lm -lSDL_mixer \
				-Wl,-framework,Cocoa -Wl,-framework,Carbon -Wl,-framework,OpenGL \
				-Wl,-framework,CoreMidi -Wl,-framework,AudioUnit \
				-Wl,-framework,AudioToolbox -Wl,-framework,IOKit -Wl,-framework,AGL \
				-Wl,-framework,QuickTime -lm
	endif
	OURASFLAGS	+= -f Mach-O
endif

ifeq ($(PLATFORM),WINDOWS)
	OURCFLAGS	+= -fno-pic -DUNDERSCORES -I$(DXROOT)/include # -I$(ENETROOT)/include
	OURASFLAGS+= -DUNDERSCORES -f win32
	LIBS		+= -L$(JAUDIOLIBDIR)/third-party/mingw32/lib -lvorbisfile -lvorbis -logg -lwsock32 -lws2_32 -lwinmm -ldsound #-L$(ENETROOT)
	GAMEOBJS+= $(OBJ)/gameres.$o $(OBJ)/winbits.$o $(OBJ)/startwin.game.$o $(OBJ)/music.$o $(OBJ)/midi.$o $(OBJ)/mpu401.$o
	EDITOROBJS+= $(OBJ)/buildres.$o
	JAUDIOLIB=libjfaudiolib_win32.a
else
	ifeq ($(RENDERTYPE),SDL)
		ifeq (0,$(SDL_FRAMEWORK))
			OURCFLAGS+= $(subst -Dmain=SDL_main,,$(shell $(SDLCONFIG) --cflags))
	        LIBS+= -lSDL_mixer
		else
			OURCFLAGS	+= -DSDL_FRAMEWORK
		endif

		ifeq (1,$(HAVE_GTK2))
			OURCFLAGS+= -DHAVE_GTK2 $(shell pkg-config --cflags gtk+-2.0)
			GAMEOBJS+= $(OBJ)/game_banner.$o $(OBJ)/startgtk.game.$o
			EDITOROBJS+= $(OBJ)/editor_banner.$o
		endif

		GAMEOBJS+= $(OBJ)/game_icon.$o $(OBJ)/sdlmusic.$o
		EDITOROBJS+= $(OBJ)/build_icon.$o
	else
		GAMEOBJS+= $(OBJ)/audiolib_musicstub.$o
	endif
endif

OURCFLAGS+= $(BUILDCFLAGS)

ifeq ($(PRETTY_OUTPUT),1)
.SILENT:
endif
.PHONY: clean all engine $(EOBJ)/$(ENGINELIB) $(EOBJ)/$(EDITORLIB) $(JAUDIOLIBDIR)/$(JAUDIOLIB)

# TARGETS

all: notice $(EDUKE32_TARGET) $(MAPSTER32_TARGET) $(EBACKTRACEDLL_TARGET)

all:
	$(BUILD_FINISHED)
ifneq (,$(EDUKE32_TARGET))
	@ls -l $(EDUKE32)
endif
ifneq (,$(MAPSTER32_TARGET))
	@ls -l $(MAPSTER32)
endif

notice:
	$(BUILD_STARTED)

$(EDUKE32): $(GAMEOBJS) $(EOBJ)/$(ENGINELIB) $(JAUDIOLIBDIR)/$(JAUDIOLIB)
	$(LINK_STATUS)
	if $(CC) -o $@ $^ $(OURLDFLAGS) $(LIBS) $(STDCPPLIB); then $(LINK_OK); else $(LINK_FAILED); fi
ifeq (1,$(RELEASE))
#	strip $(EDUKE32)
endif
	
$(MAPSTER32): $(EDITOROBJS) $(EOBJ)/$(EDITORLIB) $(EOBJ)/$(ENGINELIB) $(JAUDIOLIBDIR)/$(JAUDIOLIB)
	$(LINK_STATUS)
	if $(CC) $(CFLAGS) $(OURCFLAGS) -o $@ $^ $(OURLDFLAGS) $(LIBS) $(STDCPPLIB); then $(LINK_OK); else $(LINK_FAILED); fi
ifeq (1,$(RELEASE))	
	strip $(MAPSTER32)
endif	

duke3d_w32$(EXESUFFIX): $(OBJ)/wrapper.$o
	$(LINK_STATUS)
	if $(CC) $(CFLAGS) $(OURCFLAGS) -o $@ $^ $(OURLDFLAGS) -Wl; then $(LINK_OK); else $(LINK_FAILED); fi
ifeq (1,$(RELEASE))	
	strip duke3d_w32$(EXESUFFIX)
endif	

include Makefile.deps

ifneq (0,$(RANCID_NETWORKING))
	duke3d_h+=$(EINC)/mmulti_unstable.h
else
	duke3d_h+=$(EINC)/mmulti.h
endif

.PHONY: enginelib editorlib
enginelib editorlib:
	-mkdir -p $(EOBJ)
ifeq ($(PRETTY_OUTPUT),1)	
	printf "\033[K\033[0;35mChanging dir to \033[1;35m$(CURDIR)/$(EROOT)\033[0;35m \033[0m\n"
endif	
	$(MAKE) -C $(EROOT)/ "OBJ=../$(EOBJ)" \
		SUPERBUILD=$(SUPERBUILD) POLYMOST=$(POLYMOST) \
		USE_OPENGL=$(USE_OPENGL) BUILD32_ON_64=$(BUILD32_ON_64) \
		NOASM=$(NOASM) RELEASE=$(RELEASE) OPTLEVEL=$(OPTLEVEL) RANCID_NETWORKING=$(RANCID_NETWORKING) $@
ifeq ($(PRETTY_OUTPUT),1)		
	printf "\033[K\033[0;35mChanging dir to \033[1;35m$(CURDIR)\033[0;35m \033[0m\n"
endif	
	
$(EOBJ)/$(ENGINELIB): enginelib
$(EOBJ)/$(EDITORLIB): editorlib

$(JAUDIOLIBDIR)/$(JAUDIOLIB):
ifeq ($(PRETTY_OUTPUT),1)	
	printf "\033[K\033[0;35mChanging dir to \033[1;35m$(CURDIR)/$(JAUDIOLIBDIR)\033[0;35m \033[0m\n"
endif	
	$(MAKE) -C $(JAUDIOLIBDIR) PRETTY_OUTPUT=$(PRETTY_OUTPUT) EROOT=$(EROOT) RELEASE=$(RELEASE) OPTLEVEL=$(OPTLEVEL) 
ifeq ($(PRETTY_OUTPUT),1)		
	printf "\033[K\033[0;35mChanging dir to \033[1;35m$(CURDIR)\033[0;35m \033[0m\n"
endif

# RULES
$(EBACKTRACEDLL): Windows/src/backtrace.c
	if $(CC) -O2 -shared -Wall -Wextra -I$(EINC) -o $@ $^ -lbfd -liberty -limagehlp; then $(COMPILE_OK); else $(COMPILE_FAILED); fi

$(OBJ)/%.$o: $(SRC)/%.nasm
	$(COMPILE_STATUS)
	nasm $(OURASFLAGS) $< -o $@
 
$(OBJ)/%.$o: $(SRC)/%.c
	$(COMPILE_STATUS)
	if $(CC) $(CFLAGS) $(OURCFLAGS) -c $< -o $@; then $(COMPILE_OK); else $(COMPILE_FAILED); fi

$(OBJ)/%.$o: $(SRC)/%.cpp
	$(COMPILE_STATUS)
	if $(CXX) $(CXXFLAGS) $(OURCXXFLAGS) $(OURCFLAGS) -c $< -o $@; then $(COMPILE_OK); else $(COMPILE_FAILED); fi

$(OBJ)/%.$o: $(SRC)/jmact/%.c
	$(COMPILE_STATUS)
	if $(CC) $(CFLAGS) $(OURCFLAGS) -c $< -o $@; then $(COMPILE_OK); else $(COMPILE_FAILED); fi

$(OBJ)/%.$o: $(SRC)/misc/%.rc
	$(COMPILE_STATUS)
	if windres -i $< -o $@ --include-dir=$(EINC) --include-dir=$(SRC); then $(COMPILE_OK); else $(COMPILE_FAILED); fi

$(OBJ)/%.$o: $(SRC)/util/%.c
	$(COMPILE_STATUS)
	if $(CC) $(CFLAGS) $(OURCFLAGS) -c $< -o $@; then $(COMPILE_OK); else $(COMPILE_FAILED); fi
 
$(OBJ)/%.$o: $(RSRC)/%.c
	$(COMPILE_STATUS)
	if $(CC) $(CFLAGS) $(OURCFLAGS) -c $< -o $@; then $(COMPILE_OK); else $(COMPILE_FAILED); fi
 
$(OBJ)/game_banner.$o: $(RSRC)/game_banner.c
$(OBJ)/editor_banner.$o: $(RSRC)/editor_banner.c
$(RSRC)/game_banner.c: $(RSRC)/game.bmp
	echo "#include <gdk-pixbuf/gdk-pixdata.h>" > $@
	gdk-pixbuf-csource --extern --struct --raw --name=startbanner_pixdata $^ | sed 's/load_inc//' >> $@
$(RSRC)/editor_banner.c: $(RSRC)/build.bmp
	echo "#include <gdk-pixbuf/gdk-pixdata.h>" > $@
	gdk-pixbuf-csource --extern --struct --raw --name=startbanner_pixdata $^ | sed 's/load_inc//' >> $@

# PHONIES	

clean:
	-rm -f $(OBJ)/* $(EDUKE32) $(MAPSTER32) core* duke3d_w32$(EXESUFFIX) && $(MAKE) -C $(JAUDIOLIBDIR) clean
	-echo -n "" > $(OBJ)/keep.me

veryclean: clean
	-rm -f $(EOBJ)/* $(EBACKTRACEDLL)
	-echo -n "" > $(EOBJ)/keep.me
