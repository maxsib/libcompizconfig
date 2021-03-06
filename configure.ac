AC_PREREQ(2.57)

AC_INIT([libcompizconfig], [0.8.17], [chat-to-me@raveit.de])
AM_INIT_AUTOMAKE([1.9 foreign tar-ustar dist-xz no-dist-gzip check-news])

AC_PROG_CXX

AC_CONFIG_HEADER([config.h])
AM_MAINTAINER_MODE

AC_ISC_POSIX
AC_PROG_CC
AC_PROG_CPP
LT_INIT
AC_HEADER_STDC
AC_CHECK_HEADERS([stdlib.h sys/time.h unistd.h])
AC_CONFIG_MACRO_DIR([m4])

if test "x$GCC" = "xyes"; then
  case " $CFLAGS " in
  *[[\ \	]]-Wall[[\ \	]]*) ;;
  *) CFLAGS="$CFLAGS -Wall" ;;
  esac

  case " $CFLAGS " in
  *[[\ \	]]-Wpointer-arith[[\ \	]]*) ;;
  *) CFLAGS="$CFLAGS -Wpointer-arith" ;;
  esac

  case " $CFLAGS " in
  *[[\ \	]]-Wstrict-prototypes[[\ \	]]*) ;;
  *) CFLAGS="$CFLAGS -Wstrict-prototypes" ;;
  esac

  case " $CFLAGS " in
  *[[\ \	]]-Wmissing-prototypes[[\ \	]]*) ;;
  *) CFLAGS="$CFLAGS -Wmissing-prototypes" ;;
  esac

  case " $CFLAGS " in
  *[[\ \	]]-Wmissing-declarations[[\ \	]]*) ;;
  *) CFLAGS="$CFLAGS -Wmissing-declarations" ;;
  esac

  case " $CFLAGS " in
  *[[\ \	]]-Wnested-externs[[\ \	]]*) ;;
  *) CFLAGS="$CFLAGS -Wnested-externs" ;;
  esac

  case " $CFLAGS " in
  *[[\ \	]]-fno-strict-aliasing[[\ \	]]*) ;;
  *) CFLAGS="$CFLAGS -fno-strict-aliasing" ;;
  esac

  if test "x$enable_ansi" = "xyes"; then
    case " $CFLAGS " in
    *[[\ \	]]-ansi[[\ \	]]*) ;;
    *) CFLAGS="$CFLAGS -ansi" ;;
    esac

    case " $CFLAGS " in
    *[[\ \	]]-pedantic[[\ \	]]*) ;;
    *) CFLAGS="$CFLAGS -pedantic" ;;
    esac
  fi
fi

AC_C_BIGENDIAN

PKG_CHECK_MODULES(LIBX11, "x11")
PKG_CHECK_MODULES(COMPIZ, "compiz")

if test -z "$PKG_CONFIG"; then
    AC_PATH_PROG(PKG_CONFIG, pkg-config, no)
fi
if test "$PKG_CONFIG" != "no" ; then
	PLUGINDIR=`$PKG_CONFIG --variable=libdir compiz`/compiz
	AC_SUBST(PLUGINDIR)
	METADATADIR=`$PKG_CONFIG --variable=prefix compiz`/share/compiz
	AC_SUBST(METADATADIR)
fi

plugindir=$libdir/compiz
AC_SUBST(plugindir)

PKG_CHECK_MODULES(LIBXML2, "libxml-2.0")
AC_SUBST(LIBXML2_REQUIRES)

AC_ARG_ENABLE(protobuf,
  [  --disable-protobuf      Disable Protocol Buffers],
  [use_protobuf=$enableval], [use_protobuf=yes])

if test "x$use_protobuf" = "xyes"; then
  protobuf_error_msg="\nWARNING: You need to install Protocol Buffers to get faster program startup.\n        Sources are available at https://developers.google.com/protocol-buffers\n         Package names for Debian/Ubuntu: libprotobuf0, libprotobuf-dev, protobuf-compiler\n         Package names for other distributions: protobuf, protobuf-devel\n        Disabling protobuf.\n"
  AC_CHECK_PROG(have_protoc, protoc, yes, no)
  if test "x$have_protoc" = "xno"; then
    echo -e $protobuf_error_msg; use_protobuf=no
  else
    PKG_CHECK_MODULES(PROTOBUF, "protobuf", [], [
      echo -e $protobuf_error_msg; use_protobuf=no ])
  fi
fi
AM_CONDITIONAL([USE_PROTOBUF], [test "x$use_protobuf" = "xyes"])

dnl Do we have the POSIX compatible scandir() prototype?
AC_CACHE_CHECK([whether we have the POSIX compatible scandir() prototype],
    ac_cv_cxx_scandir_posix,[
	AC_LANG_SAVE
	AC_LANG_CPLUSPLUS
	AC_TRY_COMPILE([
	  # include <dirent.h>
	  int func (const char *d, dirent ***list, void *sort) {
	    int n = scandir(d, list, 0, (int(*)(const dirent **, const dirent **))sort);
	  }
	],[
	], ac_cv_cxx_scandir_posix=yes, ac_cv_cxx_scandir_posix=no)
	AC_LANG_RESTORE
    ])

if test "$ac_cv_cxx_scandir_posix" = yes; then
    AC_DEFINE([HAVE_SCANDIR_POSIX], [], [POSIX Compatible scandir()])
fi

AC_CHECK_HEADERS([sys/inotify.h], [have_inotify=yes], [have_inotify=no])

AC_ARG_ENABLE(debug,
  [  --enable-debug[=none,normal,full]    Enable output of debug messages],
  [enable_debug=$enableval], [enable_debug=none])

if test "x$enable_debug" = "xnone"; then
	AC_DEFINE(DEBUGLEVEL, 0, [Debug level])
elif test "x$enable_debug" = "xfull"; then
	AC_DEFINE(DEBUGLEVEL, 2, [Debug level])
else
	AC_DEFINE(DEBUGLEVEL, 1, [Debug level])
fi

AC_CONFIG_FILES([
libcompizconfig.pc
Makefile
src/Makefile
backend/Makefile
plugin/Makefile
include/Makefile
metadata/Makefile
config/Makefile
])

AC_OUTPUT

if test "x$have_inotify" = xno; then
    echo ""
    echo "NOTE: "
    echo "======"
    echo ""
    echo "Inotify header files not found. While this library might work" \
         "without them, user experience is degraded as settings won't be" \
	 "applied instantly. We strongly suggest installing the inotify" \
	 "header files."
fi
