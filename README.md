
<!-- Картинка -->
<p align="center">
<img src="https://adonius.club/uploads/posts/2022-05/1653782388_69-adonius-club-p-kot-programmist-krasivo-foto-74.jpg" width="5000px"/>
</p>

Этот репозиторий создан для подготовки к зачету по предмету: "Паттерны проектирования, продвинутое программирование C++". Вот [тут](https://htmlacademy.ru/blog/html/markdown) ты можешь краткий гайд по markdown [(и ещё один побольше)](https://paulradzkov.com/2014/markdown_cheatsheet/)

## Список тем
* [Cmake](#Cmake)


# Cmake
* |1 билет| Cmake.
    (Написать Cmake для проекта, состоящего из main.cpp, class1.h,
    class1.cpp. Стандарт С++ - 17.)
    ```Cmake
    cmake_minimum_required(VERSION "nomber")    # Минимальная потдерживаемая версия Cmake
    project(PROJECT)                            # Задает имя проекта и сохраняет его в переменной 
    set(CMAKE_CXX_STANDARD 17)                  # Установка используемого стандарта C++
    
    set(SOURCES                                 # Обьявление переменных SOURCES и HEADERS
	    main.cpp
        class1.cpp
    )
    set(HEADERS
        class1.h 
    )

    add_executable(${PROJECT_NAME}              # Добавляет задачу сборки с именем ${PROJECT_NAME}, 
                   ${SOURCES}                   # который будет создан из исходных файлов,  
                   ${HEADERS})                  # перечисленных при вызове команды.
    ```
    
* |2 билет| Cmake. 
    (Алгоритм сборки библиотеки без зависимостей из исходных кодов, 
    которые содержат CmakeLists.txt.)

    Пусть есть проект библиотеки в папке **~/my_lib** и мы хотим собрать ее по адресу **~/app**, тогда алгоритм сборки будет такой:
    ```bash
    cd ~/my_lib 
    mkdir build && cd build 
    cmake .. -DCMAKE_INSTALL_PREFIX=<директория установки>
    cmake --build . --target install -- -j<кол-во потоков>
    ```

* |3 билет| Cmake. 
    (Добавление сторонней прекомпилированной библиотеки к своему проекту.)

    Для этого необходимо добавить следующие строки в CMakeLists.txt проекта 
    ```Cmake
    set(LIB_DIR "/path/to/library")                    # Путь к директории с библиотекой
    include_directories(${LIB_DIR}/include)            # Добавление пути к заголовочным файлам библиотеки
    link_directories(${LIB_DIR}/lib)                   # Добавление пути к файлам библиотеки
    target_link_libraries(<имя_цели> <имя_библиотеки>) # Линковка библиотеки (объединения объектных файлов)
    ```

    <!-- find_path(MY_LIB_INCLUDE_DIR my_lib.h "<LIB_PATH>/include/my_lib")
    find_library(MY_LIB_MODULE_NAME NAMES lib_module_name PATHS "<LIB_PATH>/lib")

    target_include_directories(${PROJECT_NAME} PRIVATE
                                      ${MY_LIB_INCLUDE_DIR}
                          )
    target_link_libraries(${PROJECT_NAME} PRIVATE ${MY_LIB_MODULE_NAME}) -->

