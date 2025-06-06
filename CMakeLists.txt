cmake_minimum_required(VERSION 3.12)

# SPDX-FileCopyrightText: 2022 Boyuan Yang <073plan@gmail.com>
# SPDX-License-Identifier: CC0-1.0 OR BSD-2-Clause

include(GNUInstallDirs)
project(manpages-zh VERSION 1.6.4.1 HOMEPAGE_URL "https://github.com/man-pages-zh/manpages-zh")

option(ENABLE_APPEND_COLOPHON "Append translation information to output man pages" ON)
option(ENABLE_ZHCN "Enable installing zh_CN man pages" ON)
option(ENABLE_ZHTW "Enable installing zh_TW man pages" ON)
option(ENABLE_PROCPS_NG_MANPAGES "Enable installing procps-ng man pages" OFF)

set(EXCLUDED_MANPAGES "" CACHE STRING "Man pages that are not installed")

if (NOT ENABLE_PROCPS_NG_MANPAGES)
    SET(PROCPS_NG_EXCLUDE "free.1;pgrep.1;pidof.1;pmap.1;ps.1;pwdx.1;slabtop.1;tload.1;sysctl.8;vmstat.8;uptime.1;w.1;watch.1;top.1;procps.3;procps_misc.3;procps_pids.3;sysctl.conf.5")
    list(APPEND EXCLUDED_MANPAGES ${PROCPS_NG_EXCLUDE})
endif()

list(REMOVE_DUPLICATES EXCLUDED_MANPAGES)

list(LENGTH EXCLUDED_MANPAGES EXCLUDED_MANPAGES_SIZE)
if (EXCLUDED_MANPAGES_SIZE GREATER 0)
    message(STATUS "Will exclude ${EXCLUDED_MANPAGES_SIZE} man pages:")
    foreach (EXCLUDED_MANPAGE ${EXCLUDED_MANPAGES})
        message(STATUS " * ${EXCLUDED_MANPAGE}")
    endforeach ()
    set(FILTER_EXCLUDED_MANPAGES 1)
else ()
    set(FILTER_EXCLUDED_MANPAGES 0)
endif ()

if (ENABLE_APPEND_COLOPHON)
    find_package(Python3 REQUIRED COMPONENTS Interpreter)
endif (ENABLE_APPEND_COLOPHON)

find_program(opencc opencc HINTS "/usr/bin/")
if (NOT opencc)
    message(FATAL_ERROR "OpenCC not detected on your system")
endif ()


## Actual handling of man pages
set(builtstamp "${CMAKE_BINARY_DIR}/.builtstamp")
add_custom_target(man ALL DEPENDS ${builtstamp})

set(MANDIR_LIST "man1" "man2" "man3" "man4" "man5" "man6" "man7" "man8" "mann")

foreach (MANDIR ${MANDIR_LIST})
    file(GLOB CURRENT_MAN_PAGES "src/${MANDIR}/*.*")
    foreach (CURRENT_MAN_PAGE ${CURRENT_MAN_PAGES})
        get_filename_component(MAN_PAGE_NAME ${CURRENT_MAN_PAGE} NAME)
        string(REGEX REPLACE "(.*)\\." "" MAN_PAGE_SECTION_RAW ${MAN_PAGE_NAME})
        string(SUBSTRING ${MAN_PAGE_SECTION_RAW} 0 1 MAN_PAGE_SECTION)
        if (${MAN_PAGE_NAME} STREQUAL "Makefile.am")
            continue()
        endif ()
        if (FILTER_EXCLUDED_MANPAGES)
            if (${MAN_PAGE_NAME} IN_LIST EXCLUDED_MANPAGES)
                message(WARNING "Skipping excluded man page ${MAN_PAGE_NAME}...")
                continue()
            endif ()
        endif (FILTER_EXCLUDED_MANPAGES)

        set(MAN_PAGE_ROOTDIR_ZHCN "${PROJECT_BINARY_DIR}/zh_CN")
        set(MAN_PAGE_ROOTDIR_ZHTW "${PROJECT_BINARY_DIR}/zh_TW")
        set(MAN_PAGE_PREPAREDIR_ZHCN "${MAN_PAGE_ROOTDIR_ZHCN}/man${MAN_PAGE_SECTION}/")
        set(MAN_PAGE_PREPAREDIR_ZHTW "${MAN_PAGE_ROOTDIR_ZHTW}/man${MAN_PAGE_SECTION}/")
        if (ENABLE_APPEND_COLOPHON)
            set(APPEND_COLOPHON_COMMAND "${CMAKE_SOURCE_DIR}/utils/append-colophon")
        else ()
            set(APPEND_COLOPHON_COMMAND "tee")
        endif ()
        set(PROCESS_OUTPUT_ZHCN ${MAN_PAGE_PREPAREDIR_ZHCN}/${MAN_PAGE_NAME})
        set(PROCESS_OUTPUT_ZHTW ${MAN_PAGE_PREPAREDIR_ZHTW}/${MAN_PAGE_NAME})
        # XXX: This is unbelievably hacky
        # https://stackoverflow.com/questions/8925396/why-does-cmake-prefixes-spaces-with-backslashes-when-executing-a-command
        set(PROCESS_COMMAND_ZHCN
            "bash" "-c" "'cat;${CURRENT_MAN_PAGE}|${APPEND_COLOPHON_COMMAND}|opencc;-c;t2s.json;>;${PROCESS_OUTPUT_ZHCN}'")
        set(PROCESS_COMMAND_ZHTW
            "bash" "-c" "'cat;${CURRENT_MAN_PAGE}|${APPEND_COLOPHON_COMMAND}|opencc;-c;s2twp.json;>;${PROCESS_OUTPUT_ZHTW}'")

        if (ENABLE_ZHCN)
            add_custom_command(OUTPUT ${PROCESS_OUTPUT_ZHCN}
                COMMAND ${CMAKE_COMMAND} -E make_directory ${MAN_PAGE_PREPAREDIR_ZHCN}
                COMMAND ${PROCESS_COMMAND_ZHCN}
            )
            add_custom_target(man_zhcn_${MAN_PAGE_NAME}_build
                DEPENDS ${PROCESS_OUTPUT_ZHCN}
            )
            add_dependencies(man man_zhcn_${MAN_PAGE_NAME}_build)
        endif (ENABLE_ZHCN)
        if (ENABLE_ZHTW)
            add_custom_command(OUTPUT ${PROCESS_OUTPUT_ZHTW}
                COMMAND ${CMAKE_COMMAND} -E make_directory ${MAN_PAGE_PREPAREDIR_ZHTW}
                COMMAND ${PROCESS_COMMAND_ZHTW}
            )
            add_custom_target(man_zhtw_${MAN_PAGE_NAME}_build
                DEPENDS ${PROCESS_OUTPUT_ZHTW}
            )
            add_dependencies(man man_zhtw_${MAN_PAGE_NAME}_build)
        endif (ENABLE_ZHTW)

    endforeach ()
endforeach ()

# Build finish stamp
add_custom_command(OUTPUT ${builtstamp}
    COMMAND ${CMAKE_COMMAND} -E touch ${builtstamp})


if (ENABLE_ZHCN)
    install(FILES ${MAN_PAGE_ROOTDIR_ZHCN} DESTINATION "${CMAKE_INSTALL_MANDIR}/")
endif()
if (ENABLE_ZHTW)
    install(FILES ${MAN_PAGE_ROOTDIR_ZHTW} DESTINATION "${CMAKE_INSTALL_MANDIR}/")
endif()
