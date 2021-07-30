include $(srcdir)/../manpages

mandir = @mandir@/zh_TW

if APPEND_COLOPHON
APPEND_COLOPHON = ${top_srcdir}/utils/append-colophon
else !APPEND_COLOPHON
APPEND_COLOPHON = tee
endif

man_MANS = $(MANPAGES)

%.3: $(srcdir)/../%.3
	$(APPEND_COLOPHON) < $< | opencc -c s2twp.json > $@

clean-local:
	rm -f $(man_MANS)
