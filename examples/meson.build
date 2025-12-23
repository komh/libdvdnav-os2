# Copyright (C) 2025 Rob Hall
# SPDX-License-Identifier: MIT

if not get_option('enable_examples')
    subdir_done()
endif

executable('menus', 'menus.c',
    dependencies: [dvdnav_dep, dvdread_dependency])
