executable('decode_file', 'decode_file.c',
  install: false,
  dependencies : [quiet_dep, sndfile_dep],
  c_args: quiet_c_args,
)
executable('encode_file', 'encode_file.c',
  install: false,
  dependencies : [quiet_dep, sndfile_dep],
  c_args: quiet_c_args,
)

if portaudio_dep.found()
  executable('decode_soundcard', 'decode_soundcard.c',
    install: false,
    dependencies : [quiet_dep, portaudio_dep],
    c_args: quiet_c_args,
  )
  executable('encode_soundcard', 'encode_soundcard.c',
    install: false,
    dependencies : [quiet_dep, portaudio_dep],
    c_args: quiet_c_args,
  )
endif
