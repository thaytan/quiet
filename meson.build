project('quiet', 'c',
  default_options: 'c_std=gnu99', version : '1.0.0',
  meson_version : '>= 0.51.0')

cc = meson.get_compiler('c')

quiet_inc = include_directories(['include'])
quiet_c_args = [ '-Wpedantic', '-Wall', '-D_XOPEN_SOURCE=700' ]

thread_dep = dependency('threads', required: false)
libm_dep = cc.find_library('m', required : true)

jansson_dep = dependency('jansson', required : false)
if not jansson_dep.found()
  error('''
    libquiet requires libjansson but cannot find it
    fetch the latest jansson release from http://www.digip.org/jansson/
    and install it before continuing''')
endif

# liquid_dep = dependency ('liquid', required: false)
liquid_dep = cc.find_library ('liquid', required: false)
have_liquid_devel = liquid_dep.found() and cc.has_function ('flexframesync_set_header_len', dependencies: liquid_dep) and \
    cc.has_function ('flexframegen_set_header_len', dependencies: liquid_dep) and \
    cc.has_function ('ofdmflexframesync_set_header_len', dependencies: liquid_dep) and \
    cc.has_function ('ofdmflexframegen_set_header_len', dependencies: liquid_dep) and \
    cc.has_function ('resamp_rrrf_execute_output_block', dependencies: liquid_dep) and \
    cc.has_function ('liquid_pack_array_block', dependencies: liquid_dep)

if not have_liquid_devel
  cmake = import('cmake')
  message ('libliquid not found, or not the devel version - using subproject')
  warning ('The libliquid cmake subproject doesn\'t work yet. Please install manually')
  liquid_project = cmake.subproject ('liquid')
  liquid_dep = liquid_project.dependency ('liquid-shared')
endif

quiet_deps = [ jansson_dep, liquid_dep, libm_dep ]
quiet_sources = [ 'src/demodulator.c', 'src/modulator.c', 'src/utility.c', 'src/decoder.c', 'src/encoder.c', 'src/profile.c', 'src/error.c' ]
quiet_headers = [ 'include/quiet.h' ]

profiles_dir = get_option('prefix') / get_option('datadir') / 'quiet'
install_data('quiet-profiles.json', install_dir : profiles_dir)
quiet_c_args += '-DQUIET_PROFILES_LOCATION="' + profiles_dir / 'quiet-profiles.json"'

if thread_dep.found()
    quiet_sources += 'src/ring_blocking.c'
    quiet_deps += thread_dep
    quiet_c_args += [ '-DRING_BLOCKING=1', '-DQUIET_PTHREAD_ERROR=1' ]
else
    quiet_sources += 'src/ring.c'
    quiet_c_args += [ '-DRING_BLOCKING=0', '-DQUIET_PTHREAD_ERROR=0' ]
endif

portaudio_dep = dependency('portaudio', required: get_option('portaudio'))
if portaudio_dep.found()
  deps += portaudio_dep
  quiet_headers = [ 'include/quiet-portaudio.h' ]
else
  message('Building without PortAudio support')
endif

install_headers(quiet_headers, subdir : 'quiet')

libquiet = library('quiet', quiet_sources,
        c_args: quiet_c_args,
        dependencies: quiet_deps,
        include_directories: quiet_inc,
        install: true,
)

quiet_dep = declare_dependency(link_with : libquiet,
  include_directories : quiet_inc,
  dependencies : quiet_deps
)

if not get_option('programs').disabled()
  sndfile_dep = dependency('sndfile')
  subdir('programs')
endif
