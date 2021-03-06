# Copyright © 2017 Intel Corporation

# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:

# The above copyright notice and this permission notice shall be included in
# all copies or substantial portions of the Software.

# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.

es1_glapi_mapi_tmp_h = custom_target(
  'es1_glapi_mapi_tmp.h',
  input : [glapi_gen_mapi_py, glapi_gen_gl_xml],
  output : 'glapi_mapi_tmp.h',
  command : [prog_python, '@INPUT0@', 'glesv1', '@INPUT1@'],
  depend_files : glapi_gen_mapi_deps,
  capture : true,
)

_es1_c_args = []
if with_platform_windows
  _es1_c_args += ['-D_GDI32_', '-DBUILD_GL32']
endif

libglesv1_cm = shared_library(
  'GLESv1_CM' + get_option('gles-lib-suffix'),
  ['../entry.c', es1_glapi_mapi_tmp_h],
  c_args : [
    c_msvc_compat_args,
    c_vis_args,
    _es1_c_args,
    '-DMAPI_MODE_BRIDGE',
    '-DMAPI_ABI_HEADER="@0@"'.format(es1_glapi_mapi_tmp_h.full_path()),
    gcc_lto_quirk,
  ],
  link_args : [ld_args_gc_sections],
  include_directories : [inc_src, inc_include, inc_mapi],
  link_with : libglapi,
  dependencies : [dep_thread, dep_libdrm, dep_m, dep_dl],
  name_prefix : 'lib',
  install : true,
)

pkg.generate(
  name : 'glesv1_cm',
  filebase : 'glesv1_cm',
  description : 'Mesa OpenGL ES 1.1 CM library',
  version : meson.project_version(),
  libraries : libglesv1_cm,
  libraries_private : gl_priv_libs,
)

if with_tests and prog_nm.found()
  test(
    'es1-ABI-check',
    symbols_check,
    args : [
      '--lib', libglesv1_cm,
      '--symbols-file', files('gles1-symbols.txt'),
      '--nm', prog_nm.path(),
    ],
    suite : ['mapi'],
  )
endif
