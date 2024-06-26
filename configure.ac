AC_INIT([p44mbcd], [1.3.0], [luz@plan44.ch], [p44mbcd], [http://www.plan44.ch/])
AC_PREREQ([2.59])

AC_CONFIG_AUX_DIR(configure_aux)
AC_CONFIG_MACRO_DIR([m4])

AM_INIT_AUTOMAKE([1.10 no-define foreign subdir-objects])
AC_CONFIG_HEADERS([config.h])

AC_PROG_CXX

AC_CONFIG_FILES([Makefile])

##### OPTIONS

AC_ARG_ENABLE(
  [debug],
  [AC_HELP_STRING([--enable-debug], [Debug build with extra debugging code])]
)
AM_CONDITIONAL([DEBUG], [test "x$enable_debug" = xyes])

AC_ARG_ENABLE(
  [p44buildow],
  [AC_HELP_STRING([--enable-p44buildow], [plan44 build for openwrt targets])]
)
AM_CONDITIONAL([P44_BUILD_OW], [test "x$enable_p44buildow" = xyes])

AC_ARG_ENABLE(
  [ubus],
  [AS_HELP_STRING([--enable-ubus], [Enable ubus API support (requires libubus)])]
)
AM_CONDITIONAL([ENABLE_UBUS], [test "x$enable_ubus" = xyes])

AC_ARG_ENABLE(
  [embeddedlibmodbus],
  [AS_HELP_STRING([--enable-embeddedlibmodbus], [Enable embedded libmodbus (instead of using standard package)])]
)
AM_CONDITIONAL([EMBEDDED_LIBMODBUS], [test "x$enable_embeddedlibmodbus" = xyes])

AC_ARG_ENABLE(
  [p44buildwin],
  [AC_HELP_STRING([--enable-p44buildwin], [plan44 build for windows targets])]
)
AM_CONDITIONAL([P44_BUILD_WIN], [test "x$enable_p44buildwin" = xyes])



AC_CHECK_LIB(m, atan2, [], [AC_MSG_ERROR([Could not find math lib (m) with atan2])])
AC_CHECK_LIB(rt, clock_gettime, [], [AC_MSG_ERROR([Could not find rt lib with clock_gettime])])
AC_CHECK_LIB(dl, dlopen, [], [AC_MSG_ERROR([Could not find libdl with dlopen])])
AC_CHECK_LIB(i2c, i2c_smbus_write_byte, [], [AC_MSG_ERROR([Could not find libi2c with i2c_smbus_write_byte])])

AM_COND_IF([ENABLE_UBUS], [
  AC_CHECK_LIB(ubus, ubus_connect_ctx, [], [AC_MSG_ERROR([Could not find libubus with ubus_connect_ctx])])
  AC_CHECK_LIB(ubox, uloop_init, [], [AC_MSG_ERROR([Could not find libubox with uloop_init])])
  AC_CHECK_LIB(blobmsg_json, blobmsg_add_object, [], [AC_MSG_ERROR([Could not find libblobmsg_json with blobmsg_add_object])])
], [])

AM_COND_IF([EMBEDDED_LIBMODBUS], [], [
  AC_CHECK_LIB(modbus, modbus_rtu_set_custom_rts_ex, [], [AC_MSG_ERROR([Could not find libmodbus with modbus_rtu_set_custom_rts_ex - plan44 enhanced version!])])
])
AC_CHECK_LIB(json-c, json_tokener_get_error, [], [AC_MSG_ERROR([Could not find JSON-C / libjson0 with json_tokener_get_error supported (>=0.10)])])

AM_COND_IF([P44_BUILD_WIN], [], [
  AC_CHECK_LIB(ssl, TLSv1_client_method, [], [AC_MSG_ERROR([Could not find libssl with TLSv1_client_method])])
  AC_CHECK_LIB(crypto, SHA1_Init, [], [AC_MSG_ERROR([Could not find libcrypto with SHA1_Init])])
  AC_CHECK_LIB(sqlite3, sqlite3_open, [], [AC_MSG_ERROR([Could not find sqlite3 with sqlite3_open])])
])

PKG_PROG_PKG_CONFIG


AM_COND_IF([P44_BUILD_WIN], [], [
  PKG_CHECK_MODULES([JSONC], [json-c >= 0.13 ],
    [AC_DEFINE([HAVE_JSONC_VERSION_013], [1], [We have JSONC version 0.13 or later])],
    [PKG_CHECK_MODULES([JSONC], [json-c >= 0.10 ], [], [
      AC_MSG_ERROR([$JSON_PKG_ERRORS])
    ])]
  )

  PKG_CHECK_MODULES([SQLITE3], [sqlite3], [], [
    AC_MSG_ERROR([$SQLITE3_PKG_ERRORS])
  ])
])

# Checks for header files.
AC_CHECK_HEADERS([arpa/inet.h fcntl.h inttypes.h limits.h netdb.h netinet/in.h stddef.h stdint.h stdlib.h string.h sys/ioctl.h sys/socket.h sys/time.h unistd.h sys/resource.h], [], [AC_MSG_ERROR([required system header not found])])

ASSUMED_BOOST_VER=146

BOOST_REQUIRE([1.46], [
  unset boost_cv_version
  unset boost_cv_inc_path
  BOOST_REQUIRE([1.35], [
    AC_MSG_ERROR([could not find boost on your system])
  ])
  ASSUMED_BOOST_VER=135
])

if test $ASSUMED_BOOST_VER -eq 135; then
  AC_DEFINE([BOOST_VERSION_135], [1], [boost 1.35 or higher])
else
  AC_DEFINE([BOOST_VERSION_146], [1], [boost 1.46 or higher])
fi

BOOST_BIND
BOOST_FOREACH
BOOST_FUNCTION
BOOST_SMART_PTR

AX_PTHREAD([], [ AC_MSG_ERROR([required pthread library not found]) ])

AC_OUTPUT
