# ===========================================================================
# Kernel configuration targets
# These targets are used from top-level makefile

CUSTOM_DIR := $(shell pwd)
KCONFIG_TOP = Config.in
KCONFIG_PROJECT = ToyBox
CUSTOM_NAME = $(CUSTOM_DIR)/../liteos_a_custom.config
obj = ./kconfig
PHONY += clean help oldconfig menuconfig config silentoldconfig \
	randconfig allyesconfig allnoconfig allmodconfig defconfig

menuconfig: $(obj)/mconf $(KCONFIG_TOP)
	$< $(KCONFIG_TOP)

config: $(obj)/conf $(KCONFIG_TOP)
	$< $(KCONFIG_TOP)

oldconfig: $(obj)/conf $(KCONFIG_TOP)
	$< -o $(KCONFIG_TOP)

silentoldconfig: $(obj)/conf $(KCONFIG_TOP)
	yes | $< -o $(KCONFIG_TOP) > /dev/null

randconfig: $(obj)/conf $(KCONFIG_TOP)
	$< -r $(KCONFIG_TOP) > /dev/null

allyesconfig: $(obj)/conf $(KCONFIG_TOP)
	$< -y $(KCONFIG_TOP) > /dev/null

allnoconfig: $(obj)/conf $(KCONFIG_TOP)
	$< -n $(KCONFIG_TOP) > /dev/null

customconfig: $(obj)/conf $(KCONFIG_TOP)
	$< -D $(CUSTOM_NAME) $(KCONFIG_TOP) > /dev/null

defconfig: $(obj)/conf $(KCONFIG_TOP)
	$< -D /dev/null $(KCONFIG_TOP) > /dev/null

macos_defconfig: $(obj)/conf $(KCONFIG_TOP)
	KCONFIG_ALLCONFIG=$(obj)/macos_miniconfig $< -n $(KCONFIG_TOP) > /dev/null

bsd_defconfig: $(obj)/conf $(KCONFIG_TOP)
	KCONFIG_ALLCONFIG=$(obj)/freebsd_miniconfig $< -n $(KCONFIG_TOP) > /dev/null

# Help text used by make help
help::
	@echo  '  config	  - Update current config utilising a line-oriented program'
	@echo  '  menuconfig	  - Update current config utilising a menu based program'
	@echo  '  oldconfig	  - Update current config utilising a provided .config as base'
	@echo  '  silentoldconfig - Same as oldconfig, but quietly'
	@echo  '  randconfig	  - New config with random answer to all options'
	@echo  '  defconfig	  - New config with default answer to all options'
	@echo  '                    This is the maximum sane configuration.'
	@echo  '  allyesconfig	  - New config where all options are accepted with yes'
	@echo  "                    This may not actually compile, it's a starting point"
	@echo  '                    for further configuration (probably with menuconfig)'
	@echo  '  allnoconfig	  - New config where all options are answered with no'
	@echo  '                    (NOP binary, starting point for further configuration)'
	@echo  '  macos_defconfig - Select commands known to build on macosx'
	@echo  '  bsd_defconfig   - Select commands known to build on freebsd'

# Cheesy build

SHIPPED = kconfig/zconf.tab.c kconfig/lex.zconf.c kconfig/zconf.hash.c

%.c: %.c_shipped
	@ln -s $(notdir $<) $@

gen_config.h: .config

kconfig/mconf: $(SHIPPED)
	$(HOSTCC) -o $@ kconfig/mconf.c kconfig/zconf.tab.c \
		kconfig/lxdialog/*.c -lcurses -DCURSES_LOC="<ncurses.h>" \
		-DKBUILD_NO_NLS=1 -DPROJECT_NAME=\"$(KCONFIG_PROJECT)\"

kconfig/conf: $(SHIPPED)
	$(HOSTCC) -o $@ kconfig/conf.c kconfig/zconf.tab.c -DKBUILD_NO_NLS=1 \
		-DPROJECT_NAME=\"$(KCONFIG_PROJECT)\"

clean::
	@rm -f $(wildcard kconfig/*zconf*.c) kconfig/conf kconfig/mconf
