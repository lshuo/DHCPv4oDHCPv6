SUBDIRS = . tests

pkglibexecdir = $(libexecdir)/@PACKAGE@

pkglibexec_SCRIPTS = b10-cmdctl

bin_PROGRAMS = b10-certgen

nodist_pylogmessage_PYTHON = $(PYTHON_LOGMSGPKG_DIR)/work/cmdctl_messages.py
pylogmessagedir = $(pyexecdir)/isc/log_messages/

b10_cmdctldir = $(pkgdatadir)

CERTFILES = cmdctl-keyfile.pem cmdctl-certfile.pem

b10_cmdctl_DATA = cmdctl.spec

CLEANFILES= b10-cmdctl cmdctl.pyc cmdctl.spec
CLEANFILES += $(PYTHON_LOGMSGPKG_DIR)/work/cmdctl_messages.py
CLEANFILES += $(PYTHON_LOGMSGPKG_DIR)/work/cmdctl_messages.pyc

man_MANS = b10-cmdctl.8 b10-certgen.1
DISTCLEANFILES = $(man_MANS) cmdctl-certfile.pem cmdctl-keyfile.pem
EXTRA_DIST  = $(man_MANS) b10-certgen.xml b10-cmdctl.xml cmdctl_messages.mes
EXTRA_DIST += cmdctl-accounts.csv

if GENERATE_DOCS

b10-cmdctl.8: b10-cmdctl.xml
	@XSLTPROC@ --novalid --xinclude --nonet -o $@ http://docbook.sourceforge.net/release/xsl/current/manpages/docbook.xsl $(srcdir)/b10-cmdctl.xml

b10-certgen.1: b10-certgen.xml
	@XSLTPROC@ --novalid --xinclude --nonet -o $@ http://docbook.sourceforge.net/release/xsl/current/manpages/docbook.xsl $(srcdir)/b10-certgen.xml

else

$(man_MANS):
	@echo Man generation disabled.  Creating dummy $@.  Configure with --enable-generate-docs to enable it.
	@echo Man generation disabled.  Remove this file, configure with --enable-generate-docs, and rebuild BIND 10 > $@

endif

cmdctl.spec: cmdctl.spec.pre
	$(SED) -e "s|@@SYSCONFDIR@@|$(sysconfdir)|" cmdctl.spec.pre >$@

$(PYTHON_LOGMSGPKG_DIR)/work/cmdctl_messages.py : cmdctl_messages.mes
	$(top_builddir)/src/lib/log/compiler/message \
	-d $(PYTHON_LOGMSGPKG_DIR)/work -p $(srcdir)/cmdctl_messages.mes

# this is done here since configure.ac AC_OUTPUT doesn't expand exec_prefix
b10-cmdctl: cmdctl.py $(PYTHON_LOGMSGPKG_DIR)/work/cmdctl_messages.py $(CERTFILES)
	$(SED) "s|@@PYTHONPATH@@|@pyexecdir@|" cmdctl.py >$@
	chmod a+x $@

b10_certgen_SOURCES = b10-certgen.cc
b10_certgen_CXXFLAGS = $(BOTAN_INCLUDES)
b10_certgen_LDFLAGS = $(BOTAN_LIBS)

# Generate the initial certificates immediately
cmdctl-certfile.pem: b10-certgen
	./b10-certgen -q -w

cmdctl-keyfile.pem: b10-certgen
	./b10-certgen -q -w

if INSTALL_CONFIGURATIONS

# Below we intentionally use ${INSTALL} -m 640 instead of $(INSTALL_DATA)
# because these file will contain sensitive information.
install-data-local:
	$(mkinstalldirs) $(DESTDIR)/@sysconfdir@/@PACKAGE@
	for f in $(CERTFILES) ; do	\
	  if test ! -f $(DESTDIR)$(sysconfdir)/@PACKAGE@/$$f; then	\
	    ${INSTALL} -m 640 $(srcdir)/$$f $(DESTDIR)$(sysconfdir)/@PACKAGE@/ ;	\
	  fi ;	\
	done

endif

CLEANDIRS = __pycache__

clean-local:
	rm -rf $(CLEANDIRS)
