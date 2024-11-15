if not get_option('tests')
  subdir_done()
endif

tests = [
  'artifacts',
  'boot_raw_fallback',
  'bootchooser',
  'bundle',
  'checksum',
  'config_file',
  'context',
  'dm',
  'event_log',
  'hash_index',
  'install',
  'manifest',
  'progress',
  'service',
  'signature',
  'slot',
  'stats',
  'status_file',
  'update_handler',
  'utils',
]

if get_option('network')
  tests += 'network'
endif

if get_option('streaming')
  tests += 'nbd'
endif

if jsonglibdep.found()
  tests += 'boot_switch'
endif

extra_test_sources = files([
  'common.c',
  'install_fixtures.c',
])

foreach test_name : tests
  exe = executable(
    test_name + '-test',
    test_name + '.c',
    extra_test_sources, dbus_sources,
    link_with : librauc,
    c_args : '-DTEST_SERVICES="' + meson.build_root() + '"',
    include_directories : incdir,
    dependencies : rauc_deps)

  test(
    test_name,
    exe,
    depends : rauc_binary,
    is_parallel : false,
    timeout : 240,
    protocol: 'tap',
    workdir : meson.source_root())
endforeach

fakerand = executable(
  'fakerand',
  'fakerand.c',
)

pytest = find_program('pytest', required: false)

if pytest.found()
  pytests = [
    'artifacts',
    'bundle',
    'cmdline',
    'convert',
    'encrypt',
    'extract',
    'info',
    'install',
    'mark',
    'mount',
    'replace_signature',
    'resign',
    'service',
    'sign',
    'status',
    'verify',
    'write_slot',
  ]
  foreach test_name : pytests
    test(
      'pytest-' + test_name,
      pytest,
      args: ['--basetemp', '/tmp/pytest-custom-root', '-vv', 'test_' + test_name + '.py'],
      env: ['MESON_BUILD_DIR=' + rauc_build_root],
      depends : rauc_binary,
      is_parallel : false,
      timeout : 120,
      workdir : meson.current_source_dir(),
      )
  endforeach
endif
