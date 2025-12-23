# Copyright (C) 2025 Rob Hall
# SPDX-License-Identifier: MIT

project('libdvdnav', 'c',
    version: '7.0.0',
    meson_version: '>= 0.60.0',
    default_options: ['buildtype=debugoptimized',
                      'c_std=c17',
                      'default_library=both',
                      'warning_level=2'])

dvdnav_src_root = meson.current_source_dir()
dvdnav_build_root = meson.current_build_dir()
cc = meson.get_compiler('c')

# Configuration data for config.h
cdata = configuration_data()

# Include directories
dvdnav_inc_dirs = [include_directories('.', 'src', 'src/dvdnav', 'src/vm')]
if cc.get_argument_syntax() == 'msvc'
    # extra POSIX headers not found in the Windows SDK
    dvdnav_inc_dirs += include_directories('msvc/include')
endif

# The version number for the shared library
dvdnav_soname_version = '4.4.0'

dvdnav_version = meson.project_version()
dvdnav_version_split = dvdnav_version.split('.')
cdata.set_quoted('VERSION', dvdnav_version)

#
# OS/Compiler checks and defines
#

# Arguments in test_args will be used even on feature tests
test_args = ['-D_DEFAULT_SOURCE']

if cc.get_argument_syntax() == 'msvc'
    test_args += ['-D_CRT_NONSTDC_NO_WARNINGS', '-D_CRT_SECURE_NO_WARNINGS']
elif host_machine.system() == 'freebsd'
    test_args += '-DTHREAD_SAFE'
elif host_machine.system() == 'sunos'
    test_args += '-D__EXTENSIONS__'
endif

optional_arguments = []
optional_link_arguments = []

add_project_arguments('-DHAVE_CONFIG_H', language: 'c')

# Header checks

check_headers = [
    'dlfcn.h',
    'inttypes.h',
    'memory.h',
    'stdint.h',
    'stdlib.h',
    'strings.h',
    'string.h',
    'sys/stat.h',
    'sys/types.h',
    'unistd.h',
]

foreach h : check_headers
    if cc.has_header(h)
        cdata.set('HAVE_' + h.underscorify().to_upper(), 1)
    endif
endforeach

if host_machine.endian() == 'big'
    cdata.set('WORDS_BIGENDIAN', 1)
endif

if cc.has_function('gettimeofday', prefix: '#include <sys/time.h>')
    cdata.set('HAVE_GETTIMEOFDAY', 1)
endif

# External dependencies and libraries

dvdread_dependency = dependency('dvdread', version: '>=6.0.0', fallback: 'libdvdread')

if host_machine.system() in ['windows', 'cygwin', 'android']
    thread_dependency = []
else
    thread_dependency = dependency('threads')
endif

# Compiler flag tests

# Compiler flags that should be set
# But when the compiler does not supports them
# it is not an error and silently tolerated
if cc.get_argument_syntax() != 'msvc'
    optional_arguments += [
        '-mno-ms-bitfields',
        '-Wsign-compare',
    ]
endif

add_project_arguments(cc.get_supported_arguments(optional_arguments), language: 'c')
add_project_link_arguments(cc.get_supported_link_arguments(optional_link_arguments), language: 'c')
add_project_arguments(test_args, language: 'c')

# Generate config.h
configure_file(output: 'config.h', configuration: cdata)

install_data(['AUTHORS', 'ChangeLog', 'COPYING', 'README.md', 'TODO'],
    install_dir: get_option('datadir') / 'doc/libdvdnav',
    install_tag: 'doc')

# Include subdir meson.build files
# The order is important!

subdir('src/dvdnav')

subdir('src')

subdir('examples')

subdir('doc')
