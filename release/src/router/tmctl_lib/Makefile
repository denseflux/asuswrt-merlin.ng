#
# In most cases, you only need to modify this first section.
#
LIB = libtmctl.so

OBJS = tmctl_api.o

OBJS += tmctl_api_trace.o


all install: generic_private_lib_install

clean: generic_clean
	rm -f $(INSTALL_DIR)/lib/private/$(LIB)



#
# Set our CommEngine directory (by splitting the pwd into two words
# at /userspace and taking the first word only).
# Then include the common defines under CommEngine.
# You do not need to modify this part.
#
CURR_DIR := $(shell pwd)
BUILD_DIR:=$(HND_SRC)

include $(BUILD_DIR)/make.common


ifneq ($(strip $(BUILD_FAPCTL)),)
OBJS += tmctl_fap.o
endif

ifneq ($(strip $(BUILD_RDPA_QOS)),)
OBJS += tmctl_rdpa.o tmctl_ethsw.o
endif

ifeq ($(strip $(BRCM_CHIP)),63381)
ifneq ($(strip $(BUILD_BCMTM)),)
OBJS += tmctl_bcmtm.o
endif
endif

#
# Private apps and libs are allowed to include header files from the
# private and public directories
#
# WARNING: Do not modify this section unless you understand the
# license implications of what you are doing.
#
ALLOWED_INCLUDE_PATHS := -I.\
                         -I$(BUILD_DIR)/userspace/public/include  \
                         -I$(BUILD_DIR)/userspace/public/include/$(OALDIR) \
                         -I$(BUILD_DIR)/userspace/private/include  \
                         -I$(BUILD_DIR)/userspace/private/include/$(OALDIR) \
                         -I$(BUILD_DIR)/shared/opensource/include/bcm963xx \
                         -I$(BUILD_DIR)/shared/private/include/$(OALDIR) \
                         -I$(INC_BRCMDRIVER_PRIV_PATH)/$(BRCM_BOARD) \
                         -I$(INC_BRCMDRIVER_PUB_PATH)/$(BRCM_BOARD) \
                         $(INC_RDP_FLAGS)


# For customers who manually dlopen libtmctl.so, we need to explicitly
# list the library dependencies so they are listed as DT_NEEDED in this lib
ifneq ($(strip $(BUILD_FAPCTL)),)
LOCAL_LDFLAGS :=  -L$(INSTALL_DIR)/lib -lfapctl
endif
ifneq ($(strip $(BUILD_RDPA_QOS)),)
LOCAL_LDFLAGS :=  -L$(INSTALL_DIR)/lib -lrdpactl -lethswctl
endif
ifeq ($(strip $(BRCM_CHIP)),63381)
ifneq ($(strip $(BUILD_BCMTM)),)
LOCAL_LDFLAGS :=  -L$(INSTALL_DIR)/lib -lbcmtm
endif
endif

#
# Implicit rule will make the .c into a .o
# Implicit rule is $(CC) -c $(CPPFLAGS) $(CFLAGS)
# See Section 10.2 of Gnu Make manual
# 
ifneq ($(PREBUILT_BCMBIN),1)
$(LIB): $(OBJS)
	$(CC) -shared $(BCM_LD_FLAGS) -Wl,--whole-archive,-soname,$(notdir $@) -o $@ $(OBJS) -Wl,--no-whole-archive $(LOCAL_LDFLAGS)
else
$(LIB): 
	-cp -f $(TOP)/$(shell pwd | sed 's/\(.*router\/\)//')/$(PRBM_DIR)/$@ ./
endif


#
# Include the rule for making dependency files.
# The '-' in front of the second include suppresses
# error messages when make cannot find the .d files.
# It will just regenerate them.
# See Section 4.14 of Gnu Make.
#

include $(BUILD_DIR)/make.deprules

-include $(OBJS:.o=.d)
