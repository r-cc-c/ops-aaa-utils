# This is free and unencumbered software released into the public domain.
#
# Anyone is free to copy, modify, publish, use, compile, sell, or
# distribute this software, either in source code form or as a compiled
# binary, for any purpose, commercial or non-commercial, and by any
# means.
#
# In jurisdictions that recognize copyright laws, the author or authors
# of this software dedicate any and all copyright interest in the
# software to the public domain. We make this dedication for the benefit
# of the public at large and to the detriment of our heirs and
# successors. We intend this dedication to be an overt act of
# relinquishment in perpetuity of all present and future rights to this
# software under copyright law.
#
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
# EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
# MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
# IN NO EVENT SHALL THE AUTHORS BE LIABLE FOR ANY CLAIM, DAMAGES OR
# OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE,
# ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR
# OTHER DEALINGS IN THE SOFTWARE.
#
# For more information, please refer to <http://unlicense.org>

cmake_minimum_required(VERSION 2.8)

project ("pam_tacplus")

set (LIBPAM_TACPLUS pam_tacplus)
set (LIBPAM_TACPLUS_STATIC pam_tacplus_s)

include(GNUInstallDirs)
include(FindPkgConfig)

add_definitions(-DSYSCONFDIR="${CMAKE_INSTALL_SYSCONFDIR}")

# Rules to locate needed libraries
pkg_check_modules(OVSCOMMON REQUIRED libovscommon)

set (CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=gnu99 -Wall -Werror")

include_directories(
  ${CMAKE_SOURCE_DIR}
  ${CMAKE_SOURCE_DIR}/pam_libraries/tacacs/
  ${CMAKE_SOURCE_DIR}/pam_libraries/tacacs/libtac/include
  ${CMAKE_SOURCE_DIR}/pam_libraries/tacacs/libtac/lib
  ${PROJECT_SOURCE_DIR}
  ${OVSCOMMON_INCLUDE_DIRS}
  )

# PAM libraries source files
set (SOURCES_LIB_PAM_TACACS
  pam_tacplus.c
  support.c
  author_utils.c
  lib/attrib.c
  lib/crypt.c
  lib/acct_s.c
  lib/authen_r.c
  lib/author_r.c
  lib/messages.c
  lib/author_s.c
  lib/connect.c
  lib/hdr_check.c
  lib/read_wait.c
  lib/acct_r.c
  lib/cont_s.c
  lib/authen_s.c
  lib/header.c
  )

add_library (${LIBPAM_TACPLUS} SHARED ${SOURCES_LIB_PAM_TACACS})

target_link_libraries(${LIBPAM_TACPLUS} ${OVSCOMMON_LIBRARIES}
  util crypt pthread readline audit opsutils crypto pam pam_misc )

install(TARGETS ${LIBPAM_TACPLUS}
        LIBRARY DESTINATION lib/security
       )
