AC_PREREQ(2.65)

# Release version
m4_define(gdata_version_major, 0)
m4_define(gdata_version_minor, 13)
m4_define(gdata_version_micro, 1)

# API version
# TODO: This currently isn't used in the library name; once we hit gdata_version_major=1, that should be changed
m4_define(gdata_api_version_major, 0)
m4_define(gdata_api_version_minor, 0)

AC_INIT([libgdata],[gdata_version_major.gdata_version_minor.gdata_version_micro],[https://bugzilla.gnome.org/enter_bug.cgi?product=libgdata],[libgdata],[http://live.gnome.org/libgdata])

AC_CONFIG_MACRO_DIR([m4])
AC_CONFIG_SRCDIR([gdata/gdata.h])
AC_CONFIG_HEADERS([config.h])
AC_USE_SYSTEM_EXTENSIONS

AM_INIT_AUTOMAKE([1.9 dist-bzip2 no-dist-gzip check-news subdir-objects])

# Enable silent build when available (Automake 1.11)
m4_ifdef([AM_SILENT_RULES],[AM_SILENT_RULES([yes])])

AC_PROG_CXX
AM_PROG_CC_C_O
LT_INIT([])
PKG_PROG_PKG_CONFIG

AC_PATH_PROG([GLIB_GENMARSHAL],[glib-genmarshal])
AC_PATH_PROG([GLIB_MKENUMS],[glib-mkenums])

# Requirements
GLIB_REQS=2.31.0
GIO_REQS=2.17.3
SOUP_REQS=2.37.91
OAUTH_REQS=0.9.4
GTK_REQS=2.91.2

# Before making a release, the GDATA_LT_VERSION string should be modified. The string is of the form c:r:a. Follow these instructions sequentially:
#
#  1. If the library source code has changed at all since the last update, then increment revision (‘c:r:a’ becomes ‘c:r+1:a’).
#  2. If any interfaces have been added, removed, or changed since the last update, increment current, and set revision to 0.
#  3. If any interfaces have been added since the last public release, then increment age.
#  4. If any interfaces have been removed or changed since the last public release, then set age to 0.
#
# Note that versioning started at 2:0:0 to ensure no conflicts with e-d-s' libgdata library, whose maximum version was 1:0:0
GDATA_LT_VERSION=16:0:3
AC_SUBST(GDATA_LT_VERSION)

GDATA_VERSION_MAJOR=gdata_version_major
GDATA_VERSION_MINOR=gdata_version_minor
GDATA_VERSION_MICRO=gdata_version_micro
GDATA_API_VERSION=gdata_api_version_major.gdata_api_version_minor
GDATA_API_VERSION_MAJOR=gdata_api_version_major
GDATA_API_VERSION_MINOR=gdata_api_version_minor
AC_SUBST(GDATA_VERSION_MAJOR)
AC_SUBST(GDATA_VERSION_MINOR)
AC_SUBST(GDATA_VERSION_MICRO)
AC_SUBST(GDATA_API_VERSION)
AC_SUBST(GDATA_API_VERSION_MAJOR)
AC_SUBST(GDATA_API_VERSION_MINOR)

pkg_modules="glib-2.0 >= $GLIB_REQS libxml-2.0 gthread-2.0 gio-2.0 >= $GIO_REQS libsoup-2.4 >= $SOUP_REQS oauth >= $OAUTH_REQS"
PKG_CHECK_MODULES(GDATA, [$pkg_modules])
AC_SUBST(GDATA_CFLAGS)
AC_SUBST(GDATA_LIBS)

# Optional dependencies
PKG_CHECK_MODULES(GDK_PIXBUF, gdk-pixbuf-2.0 >= 2.14, have_gdk_pixbuf=yes, have_gdk_pixbuf=no)
if test "x$have_gdk_pixbuf" = "xyes"; then
	AC_DEFINE(HAVE_GDK_PIXBUF, 1, [Defined if gdk-pixbuf is installed])
fi

AC_SUBST(GDK_PIXBUF_CFLAGS)
AC_SUBST(GDK_PIXBUF_LIBS)

PKG_CHECK_MODULES([GTK], [gtk+-3.0 >= $GTK_REQS], [have_gtk=yes], [have_gtk=no])
AM_CONDITIONAL([HAVE_GTK], [test "x$have_gtk" = "xyes"])

# GNOME support, which pulls in libsoup-gnome-2.4 to provide transparent proxy support and gnome-keyring-1 to provide non-pageable memory
AC_MSG_CHECKING(whether to build with GNOME support)
AC_ARG_ENABLE(gnome, AS_HELP_STRING([--enable-gnome], [Whether to enable GNOME support]),, enable_gnome=yes)
AC_MSG_RESULT($enable_gnome)

if test "x$enable_gnome" = "xyes"; then
	AC_DEFINE(HAVE_GNOME, 1, [Defined if GNOME support is enabled])
	PKG_CHECK_MODULES([GNOME], [libsoup-gnome-2.4 gnome-keyring-1])
fi

# Various necessary functions and headers
AC_CHECK_FUNCS([strchr])
AC_CHECK_FUNCS([strstr])
AC_CHECK_FUNCS([strtol])
AC_CHECK_FUNCS([strtoul])
AC_CHECK_HEADERS([sys/time.h])

# Internationalisation support
GETTEXT_PACKAGE=gdata
AC_DEFINE_UNQUOTED(GETTEXT_PACKAGE, ["$GETTEXT_PACKAGE"], [Define to the Gettext package name])
AC_SUBST(GETTEXT_PACKAGE)
IT_PROG_INTLTOOL([0.40.0])

# Code testing coverage with gcov
AC_MSG_CHECKING(whether to build with gcov testing)
AC_ARG_ENABLE(gcov, AS_HELP_STRING([--enable-gcov], [Whether to enable gcov testing]),, enable_gcov=no)
			
if test "x$enable_gcov" = "xyes"; then
	if test "$GCC" = "no"; then
		AC_MSG_ERROR(not compiling with gcc, which is required for gcov testing)
	fi

	GCOV_CFLAGS="-fprofile-arcs -ftest-coverage"
	GCOV_LIBS="-lgcov"

	AC_SUBST(GCOV_CFLAGS)
	AC_SUBST(GCOV_LIBS)
fi

AM_CONDITIONAL(GCOV_ENABLED, test x$enable_gcov = xyes)
AC_MSG_RESULT($enable_gcov)

# General GNOME macros
GNOME_DEBUG_CHECK
GNOME_COMPILE_WARNINGS([maximum])
GNOME_MAINTAINER_MODE_DEFINES

GOBJECT_INTROSPECTION_CHECK([0.9.7])

AC_SUBST([AM_CPPFLAGS])
AC_SUBST([AM_CFLAGS])
AC_SUBST([AM_CXXFLAGS])
AC_SUBST([AM_LDFLAGS])

GTK_DOC_CHECK([1.14],[--flavour no-tmpl])

AC_CONFIG_FILES([
Makefile
libgdata.pc
gdata/gdata-version.h
gdata/tests/Makefile
po/Makefile.in
docs/Makefile
docs/reference/Makefile
docs/reference/version.xml
])
AC_OUTPUT
