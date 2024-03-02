# Используемые решения

Для сборки проекта Chromium используется meta система сборки GN.
Файлы имеют расширение `.gn`. Изучить синтаксис `.gn` файлов можно
на [официальной страничке Google](https://chromium.googlesource.com/chromium/src/tools/gn/+/441de2f/docs/quick_start.md#Running-GN).

Для определения пакетов используются файлы `3pp.pb`.
`.pb` - расширение файлов [Protocol Buffers](https://protobuf.dev/). Модули подключаются следующим образом:

```
create {
  source {
    url {
      download_url: "https://your_link/file.zip"
      version: "1.0.0"
      extension: ".zip"
    }
    patch_version: "cr0"
    unpack_archive: true
  }
}

upload {
  pkg_prefix: "tools"
  universal: true
}
```

Такой скрипт позволяет подключить пакет, который хранится по указанному URL.
Подробнее прочитать про подключение таких зависимостей вы можете
на [страничке документации](https://chromium.googlesource.com/infra/infra/+/main/recipes/README.recipes.md#package-definitions).

# Модульная структура

#### В этом разделе описываются таргеты файлов `BUILD.gn`, интересных в контексте сборки Android

Файл [`BUILD.gn` корня проекта](https://github.com/chromium/chromium/blob/main/BUILD.gn) задает
следующие [группы](https://gn.googlesource.com/gn/+/HEAD/docs/reference.md#func_group) сборки:

- `gn-all`. Содержит все основные таргеты сборки. Данную группу не стоит использовать саму по себе, т.к. группа `all`
  содержит более полный набор таргетов, группа `gn-all` является "подмножеством" группы `all`
- `all-rust`, `rust_build_tests`. Группа содержит все таргеты для разработчиков Rust.
- `traffic_annotation_auditor_dependencies`. Запускает Traffic Annotation Auditor, когда
  изменения [CQ](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/infra/cq.md) затрагивают один из
  таргетов (`//chrome:chrome`, `//remoting/client`, `//remoting/host:host`, `//tools/traffic_annotation:annotations_xml`).
  Список таргетов меняется в зависимости от аргументов запуска (`is_android`, `is_chromeos_ash` и пр.)
- `run_web_tests`. Запуск автоматизированных тестов Web UI
- `chromedriver_group`, `blink_tests`, `blink_web_tests_support_data`, `blink_web_tests_expectations`. Группы для
  запуска [Blink](https://chromium.googlesource.com/playground/chromium-org-site/+/refs/heads/main/blink/index.md)
- `webui_closure_compile`. Актуально только для ChromeOS. Подробнее можно
  прочитать [здесь](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/closure_compilation.md)
- `optimize_gn_gen`. Группа для оптимизаций сборки, работающих под Android
- `chrome_official_builder`, `chrome_official_builder_no_unittests`. Релизная сборка Chrome
- `chromiumos_preflight`. Группа для ChromeOS
- `chromium_builder_asan`. Недокументированная группа.

Файл [`BUILD.gn` пакета `chrome`](https://github.com/chromium/chromium/blob/main/chrome/BUILD.gn) обьявляет следующие
группы:

- `assert_no_deps`, `chrome_framework_plugins`, `chrome_dump_syms`, `chrome_dsym_archive`. В данный момент это не
  рабочие группы.
- `chrome`. Группа сборки исполняемых файлов chrome
- `chrome_app_strings_bundle_data`. Группа для загрузки текстовой информации с учетом локализации.
- `angle_library`, `swiftshader_library`, `widevine_cdm_library`, `keystone_registration_framework`, `optimization_guide_library`, `verify_chrome_framework_order`.
  Группы для подключения дополнительных модулей/библиотек/фреймворков
- `dependencies`. Подключение зависимостей
- `resources`, `extra_resources`, `strings`. Сборка ресурсов
- `linux_symbols`. Подключение символов Linux и ChromeOS
- `strip_lacros_files`. Подключение
  файлов [Lacros](https://chromium.googlesource.com/chromium/src/+/main/docs/lacros.md)

Группы [файла для работы с Android](https://github.com/chromium/chromium/blob/main/chrome/android/BUILD.gn):

- `delegate_public_impl_java`, `chrome_all_java`, `chrome_public_non_pak_assets`, `chrome_public_base_module_java_test_support`, `chrome_public_base_module_java_for_test`, `delegate_public_impl_java`.
  Группы для корректной работы ядра с Java
- `trichrome_library_apk`, `monochrome_public_apk`. Группы сборки `.apk` для 64-битных систем
- `trichrome_32_minimal_apks`. Группа для отслеживания размеров бинарных файлов
- `trichrome`. Базовая
  группа [Trichrome](https://chromium.googlesource.com/chromium/src.git/+/master/docs/android_native_libraries.md#Trichrome)
- `monochrome_64_secondary_abi_lib`. Группа для сборки в [Monochrome](https://chromium.googlesource.com/chromium/src.git/+/master/docs/android_native_libraries.md#monochrome)
- `jni_headers`. Заголовки JNI для сборки Android. При изменении необходимо вручную пополнить список. 
- `android_lint`. Дополнительный таргет для линта chrome
