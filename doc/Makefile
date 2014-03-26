#!/usr/bin/make -f

URL=https://wiki.debian.org/FreedomBox/Manual/Jessie?action=raw
DBURL=https://wiki.debian.org/FreedomBox/Manual/Jessie?action=RenderAsDocbook

XMLTOOPTS=
XMLTOPDFOPTS=--with-dblatex
XMLTOHTMLOPTS=

XMLLINTOPTS = --nonet --noout  --xinclude --postvalid

all: manual-jessie.pdf manual-jessie.html

manual-jessie.pdf: manual-jessie.xml
	xmlto $(XMLTOPDFOPTS) $(XMLTOOPTS) pdf $^

manual-jessie.html: manual-jessie.xml
	xmlto $(XMLTOHTMLOPTS) $(XMLTOOPTS) html-nochunks $^

manual-jessie.xml:
	GET -H "User-Agent: make" '$(DBURL)' | \
	  sed "s%<title>%\n<title>%g" | \
          sed "s%<\/title>%\n<\/title>%g" | \
          sed "s%<section%\n\n<section%g" | \
          sed "s%<\/section>%\n<\/section>%g" | \
          sed "s%<para>%\n<para>%g" | \
          sed "s%<\/para>%\n<\/para>%g" | \
          sed "s%FIXME%\nFIXME%g" | \
          sed "s%<itemizedlist>%\n<itemizedlist>%" | \
          sed "s%<listitem>%\n<listitem>%" | \
	  cat > $@.new && mv $@.new $@

lint: manual-jessie.xml
	xmllint $(XMLLINTOPTS) $^

clean:
	$(RM) manual-jessie.pdf manual-jessie.html manual-jessie.proc
distclean: clean
	$(RM) manual-jessie.xml
