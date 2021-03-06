#
# Makefile for MicroPython for K210 project
#

.PHONY:all update_mk compile
CUR_DIR_ADDR := $(shell pwd)/

###################################
# USER OPTION
LIB_NAME ?= third_party
CROSS_COMPILE ?= 
MK_VALUE :=
OUTPUT_STATIC_LIB := $(LIB_NAME).a
CC := $(CROSS_COMPILE)gcc
CXX ?= $(CROSS_COMPILE)c++
AR ?= $(CROSS_COMPILE)AR

BUILD_VERBOSE ?= 0
ifeq ($(BUILD_VERBOSE), 1)
	VERBOSE :=
else
	VERBOSE := -s V=1
endif

####################################
# Optional options
PLATFORM ?=
###############################################################################
# IMPORT MK FILE
# sinclude mk
sinclude mk
###############################################################################
# IMPORT PLATFORM OPTIONS
sinclude $(PLATFORM).mk
###############################################################################
# COMPILE OPTIONS
CFLAGS :=
###############################################################################
# SOME FUNCTIONS
define sub_clean
	$(MAKE) -C $(1) clean -s V=1 CROSS_COMPILE=$(CROSS_COMPILE) PLATFORM=$(PLATFORM);
endef

define sub_make
	$(info =====[    ThirdParty: $(dir $(1)))
	$(MAKE) -C $(basename $(patsubst %/,%,$(patsubst ./%,%,$(dir $(1))))) $(VERBOSE) all CROSS_COMPILE=$(CROSS_COMPILE) PLATFORM=$(PLATFORM)

endef

define update_sub_mk
	if [ "$(dir $(1))" != "./" ] ; \
	then \
		$(MAKE) -C $(1) update_mk -s V=1 CROSS_COMPILE=$(CROSS_COMPILE) PLATFORM=$(PLATFORM); \
	fi;
endef
###############################################################################
GET_SUBDIRS1 := $(shell find . -maxdepth 1 -type d)
GET_SUBDIRS2 := $(basename $(patsubst ./%,%,$(GET_SUBDIRS1)))
SUBDIRS := $(GET_SUBDIRS2)

SRC_C := $(wildcard *.c)
SRC_CPP := $(wildcard *.cpp)
SRC_C_OBJ := $(SRC_C:.c=.o)
CXX_OBJ := $(SRC_CPP:.cpp=.o)
INCLUDE_FLAGS := $(addprefix -I, $(INC))
CFLAGS += $(INCLUDE_FLAGS)
$(CXX_OBJ):%.o:%.cpp
	@mkdir -p $(dir $(OUTPUT_DIR))
	@$(CXX)  -o $(OUTPUT_DIR)/$@ -c $< $(INCLUDE_FLAGS) $(CXXFLAGS) -lstdc++
$(SRC_C_OBJ):%.o:%.c
	@mkdir -p $(dir $(OUTPUT_DIR))
	@$(CC)  -o $(OUTPUT_DIR)/$@ -c $< $(INCLUDE_FLAGS) $(CFLAGS) -lstdc++

FILE_MAKEFILE := $(foreach n,$(SUBDIRS) , $(wildcard $(n)/Makefile))

all: compile
.SILENT: clean update_mk compile

compile:
	$(info =====[    ThirdParty: compile)
	$(foreach n,$(FILE_MAKEFILE),$(call sub_make,$(n)))

update_mk:
	$(info =====[    ThirdParty: update mk file)
	$(foreach n,$(FILE_MAKEFILE),$(call update_sub_mk, $(dir $(n))))
	$(shell printf '%s\n' $(MK_VALUE) > mk)
	$(shell printf '%s\n' $(foreach nn,$(foreach n,$(SUBDIRS) , $(wildcard $(n)/mk)),"-include "$(CUR_DIR_ADDR)$(nn)) >> mk)

clean:
	rm -f mk
	$(info =====[    ThirdParty: clean)
	$(foreach n,$(FILE_MAKEFILE),$(call sub_clean, $(dir $(n))))

