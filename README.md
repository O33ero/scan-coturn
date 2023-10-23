##### Анализ проекта с использованием clang static analyzer и clang tidy

1. Клонируем [исследуемый проект](https://github.com/coturn/coturn)
2. Пробуем просто скомпилировать проект
   1. Заходим в папку проекта
   2. Делаем `./configure`
   3. Делаем `make` - проект собирается
   4. Делаем `make deinstall` - проект удаляется
3. Скачиваем [Clang Static Analyzer](https://clang-analyzer.llvm.org)
   1. Если у тебя Mac OS, то устанавливай [так](https://github.com/mitchty/homebrew-clang_scan_build)
   2. Если нет, то не парься - это тебе не понадобится
4. Запускаем анализ с Clang Static Analyzer 
   1. Заходим в папку проекта (лучше переклонировать)
   2. Делаем `scan-build -k -V --use-cc=/usr/bin/cc ./configure` (рекомендовано это тоже сделать под `scan-build`)
   3. Запускаем анализ `scan-build -k -V --use-cc=/usr/bin/cc make`
   4. Интересут самые [последние строки результата анализа](./clang-static-analizer/analyze.log)
   5. Открываем [папку](./clang-static-analizer/scan-build-2023-10-23-114441-99278-1) с визуализацией отчета
   6. Запускаем отчет через [index.html](./clang-static-analizer/scan-build-2023-10-23-114441-99278-1/index.html)
5. Скачиваем [Clang Tidy](https://clang.llvm.org/extra/clang-tidy/)
   1. Если у тебя Mac OS, то устанавливается [так](https://stackoverflow.com/questions/53111082/how-to-install-clang-tidy-on-macos)
   2. Если нет, то не парься - это тебе не понадобится
6. Запускаем анализ с Clang Tidy
   1. Заходим в папку проекта (лучше переклонировать)
   2. Создаем специальный 'конфиг компиляции' с помощью `cmake . -DCMAKE_EXPORT_COMPILE_COMMANDS=ON`
   3. Создастся [специальный файлик](./clang-tidy/compile_commands.json), который понадобится дальше
   4. Создаем список всех файликов исходного кода c помощью `find . -name '*.hpp' -or -name '*.cpp' -or -name '*.h' -or -name '*.c' | awk '{print $1}' ORS=' '`
   5. Получаем [список](./clang-tidy/sources.log) всех файликов с исходным кодом, который понадобится позже
   6. Запускаем анализ с помощью `clang-tidy -p ./compile_commands.json <sources> | tee output.log`
   7. Получаем [файлик](./clang-tidy/output.log) с результатами, который понадобится позже
   8. Устанавливаем `clang-tidy-html`
      1. Скачиваем скрипт вот [тут](https://github.com/austinbhale/clang-tidy-html)
   9. Визуализируем отчет Clang Tidy
      1. Заходим в папку проекта `clang-tidy-html`
      2. Запускаем скрипт с помощью `python -m clang-html output.log -o clang.html`
      3. Получаем визуализаюцию в [html-файлике](./clang-tidy/clang.html)
7. Пишем отчет
   1. Открываем [визуализацию](./clang-static-analizer/scan-build-2023-10-23-114441-99278-1/index.html) Clang Static Analyze
      1. В файлике [index.html](./clang-static-analizer/scan-build-2023-10-23-114441-99278-1/index.html) редактируем персональные данные
         1. В переменную $USERNAME указываем имя пользователя, на котором запускался анализ
         2. В переменную $WORKING_DIRECTORY указываем абсолютный путь до проекта `coturn`
         3. В переменную $DATE указываем дату запуска анализа
      2. Внимательно смотрим чтобы в визуальной части не было упоминания моего окружения (его следы можно найти поиском по файлам строки `19789758`)
      3. Убираем чужие следы, если они вдруг остались
      4. Если нашел чужие следы, которые нужно убрать, то обязательно сообщи остальным, чтобы тоже убрали
   2. Делаем фотки для Clang Static Analizer
   3. Открываем [визуализацию](./clang-tidy/clang.html) Clang Tidy
   4. Делаем фотки для Clang Tidy
   5. Собираем все фотки в один отчет
8. Отправляем отчет Тимакову 
