SRCDIR = $(realpath .)
TOPDIR = $(realpath ..)

include $(TOPDIR)/Make.version
include $(TOPDIR)/Make.rules
include $(TOPDIR)/Make.defaults

STATICLIBTARGETS=libefiboot.a
BINTARGETS=
PCTARGETS=efiboot.pc
TARGETS=$(STATICLIBTARGETS) $(BINTARGETS) $(PCTARGETS)

LIBEFIBOOT_SOURCES = loadopt.c dp.c
LIBEFIBOOT_OBJECTS = $(patsubst %.c,%.o,$(LIBEFIBOOT_SOURCES))
ALL_SOURCES=$(LIBEFIBOOT_SOURCES) \
	$(wildcard include/efivar/*.h)

$(call deps-of,$(ALL_SOURCES)) : | deps
-include $(call deps-of,$(ALL_SOURCES))

all : $(TARGETS)

libefiboot.a : $(patsubst %.o,%.static.o,$(LIBEFIBOOT_OBJECTS))

deps : $(ALL_SOURCES)
	$(MAKE) -f $(SRCDIR)/Make.deps deps SOURCES="$(ALL_SOURCES)"

clean : 
	@rm -rfv *~ *.o *.a *.E *.so *.so.* *.pc *.bin .*.d *.map \
		makeguids guid-symbols.c include/efivar/efivar-guids.h \
		$(BINTARGETS)
	@# remove the deps files we used to create, as well.
	@rm -rfv .*.P .*.h.P *.S.P include/efivar/.*.h.P

install : all
	$(INSTALL) -d -m 755 $(DESTDIR)$(libdir)
	$(foreach x,$(LIBTARGETS), \
		$(INSTALL) -m 755 $(x) $(DESTDIR)$(libdir)/$(x).1.$(VERSION) ;\
		ln -fs $(x).1.$(VERSION) $(DESTDIR)$(libdir)/$(x).1 ;\
		ln -fs $(x).1.$(VERSION) $(DESTDIR)$(libdir)/$(x) ;\
		)
	$(INSTALL) -d -m 755 $(DESTDIR)$(PCDIR)
	$(foreach x, $(PCTARGETS), $(INSTALL) -m 644 $(x) $(DESTDIR)$(PCDIR) ;)
	$(INSTALL) -d -m 755 $(DESTDIR)$(includedir)/efivar
	$(foreach x, $(wildcard $(TOPDIR)/src/include/efivar/*.h), $(INSTALL) -m 644 $(x) $(DESTDIR)$(includedir)/efivar/$(notdir $(x));)
	$(INSTALL) -d -m 755 $(DESTDIR)$(bindir)
	$(foreach x, $(BINTARGETS), $(INSTALL) -m 755 $(x) $(DESTDIR)$(bindir);)

test : all
	$(MAKE) -C test $@

.PHONY: test deps
