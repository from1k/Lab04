# Labaratory work III
Данная лабораторная работа посвещена изучению систем автоматизации сборки проекта на примере CMake.
# Progress of work
Перед началом выполнения лабораторной работы рекомендую заранее настроить рабочее пространство. Для этого необходимо как минимум копировать репозиторий с помощью ```git clone```, а также воспользоваться командами ```alias ...``` и ```export ...``` для работы с редактором и переменными соответственно. Также перед работой стоит убедиться, что сам cmake установлен (команду для установки прикрепляю).

```bash
$ git clone https://github.com/tp-labs/lab03.git
$ export GITHUB_USERNAME=<имя_пользователя>
$ export GIST_TOKEN=<сохраненный_токен>
$ alias edit=<nano|vi|vim|subl>
$ sudo app install cmake
```

Затем приступаем к выполнению непосредственно самой работы. По ходу работы я буду давать необходимые уточнения и комментарии. Я сознательно решил не делить выполнение на 3 "стадии", как это указано в репозитории с [заданием](https://github.com/tp-labs/lab03) на GitHub. Некоторые пункты бы содержали слишком мало информации, чтобы выделять это как отдельную задачую Поэтому выполняем все последовательно и по порядку.

```bash
$ cd lab03
$ ls
CMakeLists.txt    formatter_lib            LICENSE      README.md           solver_lib
formatter_ex_lib  hello_world_application  preview.png  solver_application
```
Удостоверимся в правильности и целостности скопированного репозитория. Для этого с помощью команды ```cd ...``` перейдем в репозиторий и пропишем команду ```ls``` без аргументов. Затем приступим к изменению/добавлению CMakeLists.txt файлов. Корневой такой файл уже существует, но в конце нам нужно будет его отредактировать. Вначале добавим новые файлы в каждую из директорий.

Чтобы не прописывать постоянно команду ```cd ...```, будем использовать редактор и команду ```nano ...``` (```edit ...``` усли проводилась настройка рабочего просмтранства) с указанием где нужно создать файл CMakeLists.txt. Также можно использовать команду ```cat >> ...``` как указано в [Tutorial](https://github.com/tp-labs/lab03). Мне она кажется не совсем удобной, поэтому временно откажемся от ее использования. Перейдем к созданию файлов.

## formatter_lib/CMakeLists.txt

```bash
$ nano formatter_lib/CMakeLists.txt
```
```text
cmake_minimum_required(VERSION 3.4)
project(formatter)

add_library(formatter STATIC formatter.cpp)

target_include_directories(formatter PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
```

## formatter_ex_lib/CMakeLists.txt

```bash
$ nano formatter_ex_lib/CMakeLists.txt
```
```text
cmake_minimum_required(VERSION 3.4)
project(formatter_ex)

add_library(formatter_ex STATIC formatter_ex.cpp)

target_include_directories(formatter_ex PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})

target_link_libraries(formatter_ex formatter)
```

## solver_lib/CMakeLists.txt

```bash
$ nano solver_lib/CMakeLists.txt
```
```text
cmake_minimum_required(VERSION 3.4)
project(solver)

add_library(solver STATIC solver.cpp)

target_include_directories(solver PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
```

## hello_world_application/CMakeLists.txtformatter_lib/CMakeLists.txt

```bash
$ nano hello_world_application/CMakeLists.txt
```
```text
cmake_minimum_required(VERSION 3.4)
project(hello_world)

add_executable(hello_world hello_world.cpp)

target_link_libraries(hello_world formatter_ex)
```

## solver_application/CMakeLists.txt

```bash
$ nano solver_application/CMakeLists.txt
```
```text
cmake_minimum_required(VERSION 3.4)
project(solver_app)

add_executable(solver equation.cpp)

target_link_libraries(solver formatter_ex solver)
```

Добавив везде по CMakeLists.txt перейдем в корневую папку "lab03" и изменим исходеый файл CMakeLists.txt

## Корневой CMakeLists.txt (работаем в lab03)

```bash
$ nano CMakeLists.txt
```
```text
cmake_minimum_required(VERSION 3.4)
project(lab03)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_subdirectory(formatter_lib)
add_subdirectory(formatter_ex_lib)
add_subdirectory(solver_lib)

add_subdirectory(hello_world_application)
add_subdirectory(solver_application)
```

Теперь перейдем непосредственно к сборке нашего проекта. Перед этим я хочу поделиться одной ошибкой которую я не стал описывать здесь сразу. Также поговорим об ошибках с которыми вы можете или скорее всего сталкнётесь при выполнение лабораторной работы.

```bash
$ mkdir build
$ cd build
$ cmake ..
```
Важно! Не стоит создавать сборку непосредственно в корневой папке, лучше создать под это отдельную директорию, как указано выше.

## Error 1.
Первая ошибка с которой я сталкнулся  произошла при сборке проекта из-за того, что в CMakeLists.txt в строке project(...) было укзано имя, схожее с названием исполняемого файла. Сверху эта ошибка уже исправлена и пользователям данного гайда не стоит волноваться об этом. Однако стоит понимать, что не стоит пересекать исполняемый файл с "директорией".

```bash
$ cmake ..
CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.

-- The C compiler identification is GNU 13.3.0
-- The CXX compiler identification is GNU 13.3.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
CMake Deprecation Warning at formatter_lib/CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.

CMake Deprecation Warning at formatter_ex_lib/CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.

CMake Deprecation Warning at solver_lib/CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.

CMake Deprecation Warning at hello_world_application/CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.

CMake Deprecation Warning at solver_application/CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.

CMake Error at solver_application/CMakeLists.txt:4 (add_executable):
  add_executable cannot create target "solver" because another target with
  the same name already exists.  The existing target is a static library
  created in source directory
  "/home/from1k/from1k/workspace/lab03/solver_lib".  See documentation for
  policy CMP0002 for more details.

CMake Error at solver_application/CMakeLists.txt:6 (target_link_libraries):
  Attempt to add link library "formatter_ex" to target "solver" which is not
  built in this directory.

  This is allowed only when policy CMP0079 is set to NEW.

-- Configuring incomplete, errors occurred!
```

## Error 2.
Разберем небольшой блок дальнейших действий и разберём еще одну ошибку с которой я сталкнулся. Я уже упомянул что мы приступаем к сборке проекта. Перед этим стоит убедиться что все файлы записаны правильно и корневой CMakeLists.txt также исправлен, иначе вы сможете наблюдать еще один вид ошибки такого рода:

```bash
$ cmake ..
CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.

-- The C compiler identification is GNU 13.3.0
-- The CXX compiler identification is GNU 13.3.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done (0.4s)
CMake Error at CMakeLists.txt:10 (add_library):
  Cannot find source file:

    /home/from1k/from1k/workspace/lab03/sources/print.cpp

  Tried extensions .c .C .c++ .cc .cpp .cxx .cu .mpp .m .M .mm .ixx .cppm
  .ccm .cxxm .c++m .h .hh .h++ .hm .hpp .hxx .in .txx .f .F .for .f77 .f90
  .f95 .f03 .hip .ispc

CMake Error at CMakeLists.txt:10 (add_library):
  No SOURCES given to target: print

CMake Generate step failed.  Build files cannot be regenerated correctly.
```
Если же такое все таки случилось, то решение есть, стоит исправить корневой файл, а затем выполнить пересборку проекта.

```bash
$ rm -rf *
$ cd ..
$ ls
build           formatter_ex_lib  hello_world_application  preview.png  solver_application
CMakeLists.txt  formatter_lib     LICENSE                  README.md    solver_lib
$ nano CMakeLists.txt
$ cd build
$ cmake ..
```
Псоле внесения всех исправлений все должно сработать и должно вывестись что-то подобного вида:

```bash
$ cmake ..
CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.

-- The C compiler identification is GNU 13.3.0
-- The CXX compiler identification is GNU 13.3.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
CMake Deprecation Warning at formatter_lib/CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.

CMake Deprecation Warning at formatter_ex_lib/CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.

CMake Deprecation Warning at solver_lib/CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.

CMake Deprecation Warning at hello_world_application/CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.

CMake Deprecation Warning at solver_application/CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.

-- Configuring done (0.4s)
-- Generating done (0.0s)
-- Build files have been written to: /home/from1k/from1k/workspace/lab03/build
```

Затем выполняем ```$ cmake --build .``` и сталкиваемся с ещё родной ошибкой :)

## Error 3.

```bash
$ cmake --build .
[ 10%] Building CXX object formatter_lib/CMakeFiles/formatter.dir/formatter.cpp.o
[ 20%] Linking CXX static library libformatter.a
[ 20%] Built target formatter
[ 30%] Building CXX object formatter_ex_lib/CMakeFiles/formatter_ex.dir/formatter_ex.cpp.o
[ 40%] Linking CXX static library libformatter_ex.a
[ 40%] Built target formatter_ex
[ 50%] Building CXX object solver_lib/CMakeFiles/solver.dir/solver.cpp.o
/home/from1k/from1k/workspace/lab03/solver_lib/solver.cpp: In function ‘void solve(float, float, float, float&, float&)’:
/home/from1k/from1k/workspace/lab03/solver_lib/solver.cpp:14:21: error: ‘sqrtf’ is not a member of ‘std’; did you mean ‘strtof’?
   14 |     x1 = (-b - std::sqrtf(d)) / (2 * a);
      |                     ^~~~~
      |                     strtof
/home/from1k/from1k/workspace/lab03/solver_lib/solver.cpp:15:21: error: ‘sqrtf’ is not a member of ‘std’; did you mean ‘strtof’?
   15 |     x2 = (-b + std::sqrtf(d)) / (2 * a);
      |                     ^~~~~
      |                     strtof
gmake[2]: *** [solver_lib/CMakeFiles/solver.dir/build.make:76: solver_lib/CMakeFiles/solver.dir/solver.cpp.o] Error 1
gmake[1]: *** [CMakeFiles/Makefile2:222: solver_lib/CMakeFiles/solver.dir/all] Error 2
gmake: *** [Makefile:91: all] Error 2
```

"Это ошибка уже не в CMake, а в C++. Строка ```‘sqrtf’ is not a member of ‘std’``` говорит, что функция sqrtf не находится в ```std::``` и нет нужной подключенной библиотеки. Самым простым решением будет (и на мой взгляд правильным) исправить solver.cpp в котором и появляется эта ошибка.

## Исправленный solver.cpp
```c
#include "solver.h"
#include <stdexcept>
#include <cmath>

void solve(float a, float b, float c, float& x1, float& x2) {
    float d = (b * b) - (4 * a * c);
    if (d < 0) {
        throw std::logic_error{"error: discriminant < 0"};
    }
    x1 = (-b - std::sqrt(d)) / (2 * a);
    x2 = (-b + std::sqrt(d)) / (2 * a);
}
```
Затем выполняем пересборку, перед этим обязательно возврвщвемся в build с помощью ```cd build``` и выполняем пересборку ```cmake --build .``` для того, чтобы получить желаемое:

```bash
$ cmake --build .
[ 20%] Built target formatter
[ 40%] Built target formatter_ex
[ 50%] Building CXX object solver_lib/CMakeFiles/solver.dir/solver.cpp.o
[ 60%] Linking CXX static library libsolver.a
[ 60%] Built target solver
[ 70%] Building CXX object hello_world_application/CMakeFiles/hello_world.dir/hello_world.cpp.o
[ 80%] Linking CXX executable hello_world
[ 80%] Built target hello_world
[ 90%] Building CXX object solver_application/CMakeFiles/solver_app.dir/equation.cpp.o
[100%] Linking CXX executable solver_app
[100%] Built target solver_app
```

Теперь лишь осталось убедиться в том, что мы сделали все правильно. Для этого запустим наши файлы находясь в build.

```bash
$ ./hello_world_application/hello_world
-------------------------
hello, world!
-------------------------
$ ./solver_application/solver_app
1 2 1
-------------------------
x1 = -1.000000
-------------------------
-------------------------
x2 = -1.000000
-------------------------

```

## Build and test

```bash
$ cd ..
$ git clone https://github.com/from1k/Homework3.git
$ cd test
$ mkdir build
$ cd build
$ cmake ..
$ cmake --build .
```

## Run

```bash
./hello_world_application/hello_world
./solver_application/solver_app
```
