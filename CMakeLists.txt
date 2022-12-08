cmake_minimum_required(VERSION 3.12)

# SPDX-FileCopyrightText: 2022 Boyuan Yang <073plan@gmail.com>
# SPDX-License-Identifier: CC0-1.0 OR BSD-2-Clause

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
add_custom_target(man ALL)

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

        set(MAN_PAGE_PREPAREDIR_ZHCN "${PROJECT_BINARY_DIR}/zh_CN/man${MAN_PAGE_SECTION}/")
        set(MAN_PAGE_PREPAREDIR_ZHTW "${PROJECT_BINARY_DIR}/zh_TW/man${MAN_PAGE_SECTION}/")
        if (ENABLE_APPEND_COLOPHON)
            set(APPEND_COLOPHON_COMMAND "${CMAKE_SOURCE_DIR}/utils/append-colophon")
        else ()
            set(APPEND_COLOPHON_COMMAND "tee")
        endif ()
        set(PROCESS_OUTPUT_ZHCN ${MAN_PAGE_PREPAREDIR_ZHCN}/${MAN_PAGE_NAME})
        set(PROCESS_OUTPUT_ZHTW ${MAN_PAGE_PREPAREDIR_ZHTW}/${MAN_PAGE_NAME})
        set(PROCESS_COMMAND_ZHCN
            "bash" "-c" "'cat ${CURRENT_MAN_PAGE} | ${APPEND_COLOPHON_COMMAND} | opencc -c t2s.json > ${PROCESS_OUTPUT_ZHCN}'")
        set(PROCESS_COMMAND_ZHTW
            "bash" "-c" "'cat ${CURRENT_MAN_PAGE} | ${APPEND_COLOPHON_COMMAND} | opencc -c s2twp.json > ${PROCESS_OUTPUT_ZHTW}'")

        if (ENABLE_ZHCN)
            add_custom_target(man_zhcn_${MAN_PAGE_NAME}_dir
                COMMAND ${CMAKE_COMMAND} -E make_directory
                ${MAN_PAGE_PREPAREDIR_ZHCN})
            add_custom_target(man_zhcn_${MAN_PAGE_NAME}_build VERBATIM
                COMMAND ${PROCESS_COMMAND_ZHCN})
            add_dependencies(man man_zhcn_${MAN_PAGE_NAME}_build)
            add_dependencies(man_zhcn_${MAN_PAGE_NAME}_build man_zhcn_${MAN_PAGE_NAME}_dir)
        endif (ENABLE_ZHCN)
        if (ENABLE_ZHTW)
            add_custom_target(man_zhtw_${MAN_PAGE_NAME}_dir
                COMMAND ${CMAKE_COMMAND} -E make_directory
                ${MAN_PAGE_PREPAREDIR_ZHTW})
            add_custom_target(man_zhtw_${MAN_PAGE_NAME}_build VERBATIM
                COMMAND ${PROCESS_COMMAND_ZHTW})
            add_dependencies(man man_zhtw_${MAN_PAGE_NAME}_build)
            add_dependencies(man_zhtw_${MAN_PAGE_NAME}_build man_zhtw_${MAN_PAGE_NAME}_dir)
        endif (ENABLE_ZHTW)

    endforeach ()
endforeach ()

if (ENABLE_ZHCN)
    install(FILES ${MAN_PAGE_PREPAREDIR_ZHCN} DESTINATION "${CMAKE_INSTALL_MANDIR}/")
endif()
if (ENABLE_ZHTW)
    install(FILES ${MAN_PAGE_PREPAREDIR_ZHTW} DESTINATION "${CMAKE_INSTALL_MANDIR}/")
endif()
