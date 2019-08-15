# 
# NInvoker
#
DEL  = rd /s /q
ifeq ($(strip $(VCBIN)),) 
CC   = cl.exe
LD   = link.exe
else
CC   = $(VCBIN)\cl.exe
LD   = $(VCBIN)\link.exe
endif

ifeq ($(strip $(WINSDKBIN)),) 
RC   = rc.exe
MT   = mt.exe
else
RC   = $(WINSDKBIN)\rc.exe
MT   = $(WINSDKBIN)\mt.exe
endif

DEBUG ?= 1
WIN64 ?= 1

TOP_DIR = .
SOURCE_DIR = $(TOP_DIR)/tga-wic-codec
OUTDIR = bin

PROJECT_NAME = tga-wic-codec

#  编译选项
# 
CXXBASEFLAGS = /nologo /W4 -D_WINDOWS -D_WIN32 -D_UNICODE -DUNICODE /EHsc /Zc:wchar_t
LDBASEFLAGS = /nologo /DLL /SUBSYSTEM:WINDOWS /DEF:$(SOURCE_DIR)/tga-wic-codec.def
RCFLAGS = /nologo /d "_UNICODE" /d "UNICODE" /l 0x804
WINLIBS = Ole32.lib Shell32.lib advapi32.lib windowscodecs.lib

vpath %.hpp $(SOURCE_DIR)
vpath %.cpp $(SOURCE_DIR) $(SOURCE_DIR)/tgax $(SOURCE_DIR)/wicx
vpath %.rc $(SOURCE_DIR)

ifdef WIN64
INTDIR = x64
# arx 输出目录
COMPONENT = $(OUTDIR)/$(PROJECT_NAME).dll
CXX_PLAT_FLAGS = -D_WIN64
LD_PLAT_FLAGS = /MACHINE:X64
else
INTDIR = x86
# arx 输出目录
COMPONENT = $(OUTDIR)/$(PROJECT_NAME).dll
CXX_PLAT_FLAGS = -D_X86_=1
LD_PLAT_FLAGS = /MACHINE:X86
endif

ifdef DEBUG
CXXFLAGS=-D_DEBUG /MTd /Od $(CXXBASEFLAGS) $(CXX_PLAT_FLAGS)
LDFLAGS= $(LDBASEFLAGS) /DEBUG $(LD_PLAT_FLAGS)
else
CXXFLAGS=-DNDEBUG /MT /Ox $(CXXBASEFLAGS) $(CXX_PLAT_FLAGS)
LDFLAGS=$(LDBASEFLAGS) $(LD_PLAT_FLAGS)
STRIPFLAG=-s
endif


ALL:	$(COMPONENT)

$(INTDIR):
	if not exist "$(INTDIR)/$(NULL)" mkdir "$(INTDIR)"

$(OUTDIR):
	if not exist "$(OUTDIR)/$(NULL)" mkdir "$(OUTDIR)"

clean:
	if exist "$(INTDIR)/$(NULL)" $(DEL) $(INTDIR)
	@erase "$(OUTDIR)\$(PROJECT_NAME)*.*"

SOURCES = $(SOURCE_DIR)/tga-wic-codec.cpp
TGAX_SOURCES = $(SOURCE_DIR)/tgax/tgadecoder.cpp
WICX_SOURCE = $(SOURCE_DIR)/wicx/basedecoder.cpp \
              $(SOURCE_DIR)/wicx/regman.cpp

OBJS = $(addprefix $(INTDIR)/, $(addsuffix .obj,$(basename $(notdir $(SOURCES)))))
TGAX_OBJS = $(addprefix $(INTDIR)/, $(addsuffix .obj,$(basename $(notdir $(TGAX_SOURCES)))))
WICX_OBJS = $(addprefix $(INTDIR)/, $(addsuffix .obj,$(basename $(notdir $(WICX_SOURCE)))))

$(COMPONENT): $(INTDIR) $(OUTDIR) $(INTDIR)/stdafx.obj $(OBJS) $(TGAX_OBJS) $(WICX_OBJS)
	@echo linking $@ ...
	@$(LD) $(LDFLAGS) /out:$@ $(OBJS) $(TGAX_OBJS) $(WICX_OBJS) $(WINLIBS)
	@if exist $(COMPONENT).manifest $(MT) -nologo -manifest $(COMPONENT).manifest -outputresource:$(COMPONENT);2

$(INTDIR)/stdafx.obj:stdafx.cpp stdafx.hpp
	@$(CC) $(CXXFLAGS) -c /Yc"stdafx.hpp" /Fp$(INTDIR)/$(PROJECT_NAME).pch /Fo$@ $<

$(OBJS): stdafx.hpp

$(INTDIR)/%.obj:%.cpp stdafx.hpp
	@$(CC) $(CXXFLAGS) -c -Fo$@ /Yu"stdafx.hpp" /Fp$(INTDIR)/$(PROJECT_NAME).pch $<

$(INTDIR)/%.obj:%.c
	@$(CC) $(CXXFLAGS) -c -Fo$@ $<

$(INTDIR)/%.res:%.rc
	@echo compiling resources
	@$(RC) $(RCFLAGS) /fo"$@" $<

$(INTDIR)/$(PROJECT_NAME).res:$(PROJECT_NAME).rc
