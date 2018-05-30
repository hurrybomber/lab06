## Laboratory work VI

Данная лабораторная работа посвещена изучению фреймворков для тестирования на примере **Catch**

```ShellSession
$ open https://github.com/philsquared/Catch
```

## Tasks

- [ ] 1. Создать публичный репозиторий с названием **lab06** на сервисе **GitHub**
- [ ] 2. Выполнить инструкцию учебного материала
- [ ] 3. Ознакомиться со ссылками учебного материала
- [ ] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```ShellSession
$ export GITHUB_USERNAME=hurrybomber //создать переменную
$ alias gsed=sed # for *-nix system //создаем псевдоним команды
```

```ShellSession
$ cd ${GITHUB_USERNAME}/workspace //переходим в каталог
$ pushd . //сохраняем текующий каталог в памяти
~/hurrybomber/workspace ~/hurrybomber/workspace
$ source scripts/activate //выполняем скрипты
```

```ShellSession
$ git clone https://github.com/${GITHUB_USERNAME}/lab05 projects/lab06 //клонируем содержимое репозитория в папку
Cloning into 'projects/lab06'...
remote: Counting objects: 31, done.
remote: Compressing objects: 100% (19/19), done.
remote: Total 31 (delta 7), reused 27 (delta 6), pack-reused 0
Unpacking objects: 100% (31/31), done.

$ cd projects/lab06 //переходим в каталог
$ git remote remove origin //управление репозиторием
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab06 //новое имя удаленному репозиторию
```

```ShellSession
$ mkdir tests //создаем каталог
$ wget https://github.com/philsquared/Catch/releases/download/v1.9.3/catch.hpp -O tests/catch.hpp //скачиваем catch.hpp
--2018-04-19 13:17:17--  https://github.com/philsquared/Catch/releases/download/v1.9.3/catch.hpp
HTTP-запрос отправлен. Ожидание ответа… 200 OK
Длина: 417675 (408K) [application/octet-stream]
Сохранение в: «tests/catch.hpp»

tests/catch.hpp     100%[===================>] 407,89K   286KB/s    за 1,4s    

2018-04-16 12:28:20 (286 KB/s) - «tests/catch.hpp» сохранён [417675/417675]


$ cat > tests/main.cpp <<EOF // записываем строки в файл
#define CATCH_CONFIG_MAIN
#include "catch.hpp"
EOF
```

```ShellSession
$ gsed -i '/option(BUILD_EXAMPLES "Build examples" OFF)/a\ //изменить [поток] файл (без перемещения) + резервные копии
option(BUILD_TESTS "Build tests" OFF)
' CMakeLists.txt
$ cat >> CMakeLists.txt <<EOF //записать строки в файл

if(BUILD_TESTS)
	enable_testing()
	file(GLOB \${PROJECT_NAME}_TEST_SOURCES tests/*.cpp)
	add_executable(check \${\${PROJECT_NAME}_TEST_SOURCES})
	target_link_libraries(check \${PROJECT_NAME} \${DEPENDS_LIBRARIES})
	add_test(NAME check COMMAND check "-s" "-r" "compact" "--use-colour" "yes") 
endif()
EOF
```

```ShellSession
$ cat >> tests/test1.cpp <<EOF //записать строки в файл
#include "catch.hpp"
#include <print.hpp>

TEST_CASE("output values should match input values", "[file]") {
  std::string text = "hello";
  std::ofstream out("file.txt");
  
  print(text, out);
  out.close();
  
  std::string result;
  std::ifstream in("file.txt");
  in >> result;
  
  REQUIRE(result == text);
}
EOF
```

```ShellSession
$ cmake -H. -B_build -DBUILD_TESTS=ON //подготовка к построению проекта, создание каталога для сборки, проверка на работоспособность
-- The C compiler identification is AppleClang 9.1.0.9020039
-- The CXX compiler identification is AppleClang 9.1.0.9020039
-- Check for working C compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/cc
-- Check for working C compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /Users/mac/WhiteRabbitRo/workspace/projects/lab06/_build

$ cmake --build _build //построене проекта
Scanning dependencies of target print
[ 20%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 40%] Linking CXX static library libprint.a
[ 40%] Built target print
Scanning dependencies of target check
[ 60%] Building CXX object CMakeFiles/check.dir/tests/main.cpp.o
[ 80%] Building CXX object CMakeFiles/check.dir/tests/test1.cpp.o
[100%] Linking CXX executable check
[100%] Built target check

$ cmake --build _build --target test //прохождение тестов
Running tests...
Test project /Users/mac/hurrybomber/workspace/projects/lab06/_build
Start 1: check
1/1 Test #1: check ............................   Passed    0.01 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.01 sec

```

```ShellSession
$ _build/check -s -r compact //проверка построения (с параметром  compact)
/Users/hurrybomber/workspace/projects/lab06/tests/test1.cpp:15: PASSED: result == text for: "hello" == "hello"
Passed 1 test case with 1 assertion.

$ cmake --build _build --target test -- ARGS=--verbose //тесты с подробностями
Running tests...
UpdateCTestConfiguration  from :/Users/hurrybomber/workspace/projects/lab06/_build/DartConfiguration.tcl
UpdateCTestConfiguration  from :/Users/hurrybomber/workspace/projects/lab06/_build/DartConfiguration.tcl
Test project /Users/hurrybomber/workspace/projects/lab06/_build
Constructing a list of tests
Done constructing a list of tests
Updating test list for fixtures
Added 0 tests to meet fixture requirements
Checking test dependency graph...
Checking test dependency graph end
test 1
Start 1: check

1: Test command: /Users/hurrybomber/workspace/projects/lab06/_build/check "-s" "-r" "compact" "--use-colour" "yes"
1: Test timeout computed to be: 10000000
1: /Users/hurrybomber/workspace/projects/lab06/tests/test1.cpp:15: PASSED: result == text for: "hello" == "hello"
1: Passed 1 test case with 1 assertion.
1: 
1/1 Test #1: check ............................   Passed    0.01 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.01 sec

```

```ShellSession
$ gsed -i 's/lab05/lab06/g' README.md //изменить [поток] файл (без перемещения) + резервные копии
$ gsed -i 's/\(DCMAKE_INSTALL_PREFIX=_install\)/\1 -DBUILD_TESTS=ON/' .travis.yml //изменить [поток]  файл (без перемещения) + резервные копии
$ gsed -i '/cmake --build _build --target install/a\ //изменить [поток] файл (без перемещения) + резервные копии
- cmake --build _build --target test -- ARGS=--verbose
' .travis.yml
```

```ShellSession
$ travis lint //предупреждения для .travis.yml
Warnings for .travis.yml:
[x] value for addons section is empty, dropping
[x] in addons section: unexpected key apt, dropping

```

```ShellSession
$ git add . // начат отслеживать все файлы
$ git commit -m"added tests" //создать коммит
[master eb9c8cc] added tests
5 files changed, 11501 insertions(+)
create mode 100644 file.txt
create mode 100644 tests/catch.hpp
create mode 100644 tests/main.cpp
create mode 100644 tests/test1.cpp

$ git push origin master //копируем ветку master
Counting objects: 28, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (22/22), done.
Writing objects: 100% (28/28), 8.58 KiB | 731.00 KiB/s, done.
Total 28 (delta 6), reused 0 (delta 0)
remote: Resolving deltas: 100% (6/6), done.
To https://github.com/hurrybomber/lab06
* [new branch]      master -> master

```

```ShellSession
$ travis login --auto //логинимся в travis
Successfully logged in as hurrybomber!

$ travis enable //включить проект
Detected repository as hurrybomber/lab06, is this correct? |yes| yes
hurrybomber/lab06: enabled :)

```

```ShellSession
$ mkdir artifacts //создаем каталог
$ sleep 20s && gnome-screenshot --file artifacts/screenshot.png //сделать скриншоты и сохранить в папке
# for macOS: $ screencapture -T 20 artifacts/screenshot.png
# open https://github.com/${GITHUB_USERNAME}/lab06
```

## Report

```ShellSession
$ popd //возвращение на верхний стэк
~/hurrybomber/workspace
$ export LAB_NUMBER=06 //создаем переменную
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
Cloning into 'tasks/lab06'...
remote: Counting objects: 77, done.
remote: Total 77 (delta 0), reused 0 (delta 0), pack-reused 77
Unpacking objects: 100% (77/77), done.

$ mkdir reports/lab${LAB_NUMBER} //создаем каталог
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md //копируем файл
$ cd reports/lab${LAB_NUMBER} //переходим в каталог
$ edit REPORT.md //открываем отчет
$ gistup -m "lab${LAB_NUMBER}" //выгружаем гист
```

## Links

- [Boost.Tests](http://www.boost.org/doc/libs/1_63_0/libs/test/doc/html/)
- [Google Test](https://github.com/google/googletest)

```
Copyright (c) 2017 Братья Вершинины
```
