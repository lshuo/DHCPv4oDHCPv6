SUBDIRS = datasrc util cc config dns log net notify testutils acl bind10
SUBDIRS += xfrin log_messages server_common ddns sysinfo statistics

python_PYTHON = __init__.py

pythondir = $(pyexecdir)/isc

CLEANDIRS = __pycache__

clean-local:
	rm -rf $(CLEANDIRS)
