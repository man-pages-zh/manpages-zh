cmake_minimum_required(VERSION 3.12)

# SPDX-FileCopyrightText: 2022 Boyuan Yang <073plan@gmail.com>
# SPDX-License-Identifier: CC0-1.0 OR BSD-2-Clause

include(CMakePrintHelpers)
include(GNUInstallDirs)
project(manpages-zh VERSION 1.6.4.0 HOMEPAGE_URL "https://github.com/man-pages-zh/manpages-zh")

option(ENABLE_APPEND_COLOPHON "Append translation information to output man pages" ON)
option(ENABLE_ZHCN "Enable installing zh_CN man pages" ON)
option(ENABLE_ZHTW "Enable installing zh_TW man pages" ON)

set(EXCLUDED_MANPAGES "" CACHE STRING "Man pages that are not installed")

list(LENGTH EXCLUDED_MANPAGES EXCLUDED_MANPAGES_SIZE)
if (EXCLUDED_MANPAGES_SIZE GREATER 0)
    message(STATUS "Will exclude total of ${EXCLUDED_MANPAGES_SIZE} man pages.")
    set(FILTER_EXCLUDED_MANPAGES 1)
else ()
    set(FILTER_EXCLUDED_MANPAGES 0)
endif ()

if (ENABLE_APPEND_COLOPHON)
    find_package(Python3 REQUIRED COMPONENTS Interpreter)
endif (ENABLE_APPEND_COLOPHON)

find_program(opencc opencc HINTS "/usr/bin/")
if (opencc_NOTFOUND)
    message(FATAL_ERROR "OpenCC not detected on your system")
endif ()


## Actual handling of man pages
set(MANDIR_LIST "man1" "man2" "man3" "man4" "man5" "man6" "man7" "man8" "mann")

foreach (MANDIR ${MANDIR_LIST})
    file(GLOB CURRENT_MAN_PAGES "src/${MANDIR}/*.*")
    foreach (CURRENT_MAN_PAGE ${CURRENT_MAN_PAGES})
        get_filename_component(MAN_PAGE_NAME ${CURRENT_MAN_PAGE} NAME)
        if (${MAN_PAGE_NAME} STREQUAL "Makefile.am")
            continue()
        endif ()
        if (FILTER_EXCLUDED_MANPAGES)
            if (${MAN_PAGE_NAME} IN_LIST EXCLUDED_MANPAGES)
                message(WARNING "Skipping excluded man page ${MAN_PAGE_NAME}...")
                continue()
            endif ()
        endif (FILTER_EXCLUDED_MANPAGES)
        # TODO: Process and install this man page
        #        cmake_print_variables(MAN_PAGE_NAME)
    endforeach ()
endforeach ()
