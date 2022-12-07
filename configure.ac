#                                               -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.

AC_PREREQ([2.61])
AC_INIT([manpages-zh],[1.6.3.7],[https://github.com/man-pages-zh/manpages-zh/issues])
AC_CONFIG_SRCDIR([src/man1/basename.1])
AM_INIT_AUTOMAKE([subdir-objects])
AM_MAINTAINER_MODE

AC_ARG_ENABLE([append_colophon],
              [AS_HELP_STRING([--disable-append-colophon],
                              [do not append translation information
                               to output man pages])],
              [case "${enableval}" in
                    yes) append_colophon=true  ;;
                    no)  append_colophon=false ;;
                    *) AC_MSG_ERROR([bad value ${enableval} for --disable-append-colophon]) ;;
               esac],
              [append_colophon=true])
AM_CONDITIONAL([APPEND_COLOPHON], [test "x${append_colophon}" = "xtrue"])

# append-colophon require python3 to work properly
AS_IF([test "x${append_colophon}" = "xtrue"],
      [AC_CHECK_PROG([HAVE_PYTHON3], [python3], [yes], [no])
       AS_IF([test "x${HAVE_PYTHON3}" = "xno"],
             [AC_MSG_ERROR([you need to install python3 to build the package.])],
             [])
       ],
      [])

AC_CHECK_PROG([HAVE_OPENCC], [opencc], [yes], [no])
AS_IF([test "x${HAVE_OPENCC}" = "xno"],
      [AC_MSG_ERROR([you need to install opencc to build the package.])],
      [])

AC_ARG_ENABLE([zhtw],
              [AS_HELP_STRING([--disable-zhtw],
                              [do not generate manpages for zh_TW])],
              [case "${enableval}" in
                       yes) zhtw=true ;;
                no) zhtw=false ;;
                *) AC_MSG_ERROR([bad value ${enableval} for --enable-zhtw]) ;;
                esac],
              [zhtw=true]
              )
AS_IF([test "x${zhtw}" = "xtrue"],
      [zh_TW=zh_TW; AC_SUBST([zh_TW])],
      [])

AC_ARG_ENABLE([zhcn],
              [AS_HELP_STRING([--disable-zhcn],
                              [do not generate manpages for zh_CN])],
              [case "${enableval}" in
                yes) zhcn=true ;;
                no) zhcn=false ;;
                *) AC_MSG_ERROR([bad value ${enableval} for --enable-zhcn]) ;;
                esac],
              [zhcn=true]
              )
AS_IF([test "x${zhcn}" = "xtrue"],
      [zh_CN=zh_CN; AC_SUBST([zh_CN])],
      [])

AS_IF([test "x${zhcn}" = "xfalse" && test "x${zhtw}" = "xfalse" ],
      [AC_MSG_ERROR([You cannot disable zh_CN and zh_TW pages together. Exiting...])],
      [])


AC_CONFIG_FILES([Makefile
                 DOCS/Makefile
                 src/Makefile
                 src/man1/Makefile
                 src/man1/zh_CN/Makefile
                 src/man1/zh_TW/Makefile
                 src/man2/Makefile
                 src/man2/zh_CN/Makefile
                 src/man2/zh_TW/Makefile
                 src/man3/Makefile
                 src/man3/zh_CN/Makefile
                 src/man3/zh_TW/Makefile
                 src/man4/Makefile
                 src/man4/zh_CN/Makefile
                 src/man4/zh_TW/Makefile
                 src/man5/Makefile
                 src/man5/zh_CN/Makefile
                 src/man5/zh_TW/Makefile
                 src/man6/Makefile
                 src/man6/zh_CN/Makefile
                 src/man6/zh_TW/Makefile
                 src/man7/Makefile
                 src/man7/zh_CN/Makefile
                 src/man7/zh_TW/Makefile
                 src/man8/Makefile
                 src/man8/zh_CN/Makefile
                 src/man8/zh_TW/Makefile
                 src/mann/Makefile
                 src/mann/zh_CN/Makefile
                 src/mann/zh_TW/Makefile
                 utils/Makefile
                 ])
AC_OUTPUT

echo
echo "manpages-zh configure summary"
echo "============================="
echo "zh_CN   : $zhcn"
echo "zh_TW   : $zhtw"
echo "COLOPHON: $append_colophon"
echo
