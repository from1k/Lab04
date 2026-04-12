# Labaratory work IV
Данная лабораторная работа посвещена изучению систем непрерывной интеграции на примере сервиса GitHub Actions.

# Progress of work
Перед началом работы рекомендую заранее настроить рабочее пространство. В данной лабороторной работе мы используем репозиторий из предыдущей [лабароторной работы](https://github.com/tp-labs/lab04). Но для того чтобы не изменять структуры предыдущей работы, просто выполним копирование репозитория в новый. Перед этим обязательно на GitHub создадим пустой репозиторий.

```bash
$ git clone https://github.com/from1k/Homework3.git Homework4.0
$ cd Homework4.0
$ rm -rf .git
$ git init
$ git add .
$ git commit -m "Comy initial"
$ git remote add origin https://github.com/from1k/Lab04.git
$ git branch -M main
$ git push -u origin main
```

Приступаем к выполнению работы. По ходу работы я буду давать необходимые уточнения и комментарии. Для корректной работы GitHub Фсешщты первым делом нужно создать директорию .github/workflows

```bash
$ mkdir -p .github/workflows
$ cd .github/workflows
```
Затем необходимо создать файл формата ```.yml``` внутри этой директории. Он описывает автоматические действия, которые GitHub выполняет при изменениях в репозитории: сборку, тестирование и запуск программ.

```bash
$ nano ci.yml
$ cat ci.yml
name: CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:

  linux-gcc:
    runs-on: ubuntu-latest
    name: Linux (gcc)

    steps:
      - uses: actions/checkout@v4

      - name: Install dependencies
        run: sudo apt-get update && sudo apt-get install -y cmake g++

      - name: Configure
        run: cmake -B build -DCMAKE_CXX_COMPILER=g++

      - name: Build
        run: cmake --build build

      - name: Show build tree
        run: find build -type f

      - name: Run hello_world
        run: find build -type f -name "hello_world" -executable -exec {} \;

      - name: Run solver
        run: find build -type f -name "solver" -executable -exec {} \;

  linux-clang:
    runs-on: ubuntu-latest
    name: Linux (clang)

    steps:
      - uses: actions/checkout@v4

      - name: Install dependencies
        run: sudo apt-get update && sudo apt-get install -y cmake clang

      - name: Configure
        run: cmake -B build -DCMAKE_CXX_COMPILER=clang++

      - name: Build
        run: cmake --build build

      - name: Show build tree
        run: find build -type f

      - name: Run hello_world
        run: find build -type f -name "hello_world" -executable -exec {} \;

      - name: Run solver
        run: find build -type f -name "solver" -executable -exec {} \;

  windows-msvc:
    runs-on: windows-latest
    name: Windows (MSVC)

    steps:
      - uses: actions/checkout@v4

      - name: Configure
        run: cmake -B build

      - name: Build
        run: cmake --build build --config Release

      - name: Show build tree
        run: Get-ChildItem -Recurse build

      - name: Run hello_world
        shell: pwsh
        run: |
          Get-ChildItem -Recurse build -Filter hello_world.exe | Select-Object -First 1 | ForEach-Object { & $_.FullName }

      - name: Run solver
        shell: pwsh
        run: |
          Get-ChildItem -Recurse build -Filter solver.exe | Select-Object -First 1 | ForEach-Object { & $_.FullName }
```

Остановимся подробнее на содержимом ```ci.yml``` и разберемся с тем, что в нем написано. Вначале описывается общая структура файла  (CI pipeline) и то, как он запускается (в нашем случае запускается автоматически при изменениях кода). После фрагмента ```jobs:``` прописываются основные задачи CI. Мы описываем 3 независимые сборки:
- Linux GCC
- Linux Clang
- Windows MSVC

Для каждого отдельная виртуальная машина. Строка ```uses: actions/checkout@v4``` означает, что GitHub копирует репозиторий внутрь виртуальной машины. Каждый отдельный блок из ```steps```, содержащий ```name``` и ```run```, описывает какое-либо дальнейшее действие, которое будет выполнено автоматический программой (ему дается название и команда).

Теперь можем запушить все изменения в наш репозиторий. После этого необходимо зайти в наш репозитоорий на GitHub и убедиться в том, что все работает. Сразу при появлении коммита во вкладке Actions появятся действующие тесты. Если все работает и выполнено успешно, то все три теста будут помечены как выполненные. Иначе GitHub укажет на то, какие тесты не выполнились и во вкладке Details можно будет узнать подробности. Дальнейшие исправления зависят от того, в чем конкретная проблема. Например в моем случае была масштабная проблема с поиском исполняемых файлов, которую пришлось достаточно долго исправлять.

```bash
$ git add .github/workflows/ci.yml
$ git commit -m "Add GitHub Actions"
[main 260adf8] Add GitHub Actions
 1 file changed, 85 insertions(+)
 create mode 100644 .github/workflows/ci.yml
$ git push
Enumerating objects: 6, done.
Counting objects: 100% (6/6), done.
Delta compression using up to 3 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (5/5), 846 bytes | 846.00 KiB/s, done.
Total 5 (delta 1), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/from1k/Lab04.git
   64e7c5e..260adf8  main -> main
```
На этом всё! Спасибо за внимание!
