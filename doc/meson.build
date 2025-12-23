# Copyright (C) 2025 Rob Hall
# SPDX-License-Identifier: MIT

if not get_option('enable_docs')
    subdir_done()
endif

doxygen = find_program('doxygen', required: true)

doxyfile_conf = configuration_data()
doxyfile_conf.set('INPUT', f'@dvdnav_src_root@/src/dvdnav \\\n @dvdnav_build_root@/src/dvdnav')
doxyfile_conf.set('PROJECT_NAME', 'libdvdnav')
doxyfile_conf.set('STRIP_FROM_PATH', f'@dvdnav_src_root@/src \\\n @dvdnav_build_root@/src')
doxyfile_conf.set('OUTPUT_DIRECTORY', meson.current_build_dir())
doxyfile_conf.set('INPUT_DIRECTORY', meson.current_source_dir())
doxyfile_conf.set('PROJECT_VERSION', meson.project_version())

doxyfile = configure_file(
    input: 'doxy.conf',
    output: 'Doxyfile',
    configuration: doxyfile_conf,
)

custom_target('docs',
    depend_files: dvdnav_api_headers,
    command: [doxygen, '-q', doxyfile],
    output: ['html'],
    install: true,
    install_dir: get_option('datadir') / 'doc/libdvdnav',
    install_tag: 'doc',
)
