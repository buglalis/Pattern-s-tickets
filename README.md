
<!-- Картинка -->
<p align="center">
<img src="https://adonius.club/uploads/posts/2022-05/1653782388_69-adonius-club-p-kot-programmist-krasivo-foto-74.jpg" width="5000px"/>
</p>

Этот репозиторий создан для подготовки к зачету по предмету: "Паттерны проектирования, продвинутое программирование C++". Вот [тут](https://htmlacademy.ru/blog/html/markdown) ты можешь краткий гайд по markdown [(и ещё один побольше)](https://paulradzkov.com/2014/markdown_cheatsheet/)

## Список тем
* [Cmake](#Cmake)
* [Классы](#Классы)


# Cmake
* |1 билет| Cmake.
    (Написать Cmake для проекта, состоящего из main.cpp, class1.h,
    class1.cpp. Стандарт С++ - 17.)
    ```Cmake
    cmake_minimum_required(VERSION "number")    # Минимальная потдерживаемая версия Cmake
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

# Классы
* ## 28 билет
        Использование аргументов со значениями по умолчанию в виртуальных функциях

    Виртуальные функции **связываются динамически** (адреса функций определяются с помощью таблицы виртуальных функций). Обычные функции **связываются статически** (на этапе компиляции). 

    Аргументы по умолчанию тоже связываются статически, т.е. зависят от статического типа.

    То есть запуск такого кода преведёт к ошибке
    ```c++
    struct Base {
        virtual int foo(int x = 14) { return x; }
    };

    struct Derived: public Base {
        int foo(int x) override { return x;}
    };

    int main(){
            Derived d{};
            std::cout << d.foo();
    }
    ```
        >> error: no matching function for call to ‘Derived::foo()’
    Так как аргумент по у молчанию для Derived::foo(int) не определён.

    ```c++
    struct Base {
        virtual int foo(int x = 14) { return x; }
    };

    struct Derived: public Base {
        int foo(int x = 24) override { return x;}
    };

    int main(){
            Derived d{};
            std::cout << d.foo();
    }
    ```
        >> 24

    > res: лекция 1, слайд 34
* ## 29 билет
        NVI. Пример

    **NVI** - non-virtual interfase.
    Если нужен интерфейс с аргументами по умолчанию, его можно сделать невиртуальным, чтобы никто не смог их переопределить. 
    ```c++
    struct BaseNVI {
        int foo(int x = 14) { return foo_impl(x); }
    private:
        virtual int foo_impl(int a) { return a; }
    };

    struct DerivedNVI: public BaseNVI{
        int foo_impl(int a) override { return a*2;}
    };

    int main(){
        DerivedNVI d{};
        std::cout << d.foo();
    }
    ```
        >> 28
    > res: лекция 1, слайд 35
* ## 30 билет
        Может ли существовать шаблон виртуального метода?
    Нет. 
    > res: лекция 1, слайд 37

* ## 31 билет
        Можно ли перегружать виртуальные функции?
    Да.
    > res: лекция 1, слайд 37
* ## 32 билет
        Как добавить в overloading set класса-наследника методы базового класса являющиеся перегрузками виртуального метода?
    ```c++
    struct Matrix{
        virtual void pow(double x) { cout << "Matrix double: " << x << endl; }
        virtual void pow(int x) { cout << "Matrix int: " << x << endl; }
    };

    struct SparceMatrix: Matrix{
        void pow(int x) { cout << "SparceMatrix int: " << x << endl; }
    };

    int main(){
        Matrix* m = new SparceMatrix;
        m->pow(1.5);
        m->pow(4);
        SparceMatrix sm;
        sm.pow(1.5);
        sm.pow(4);
    }
    ```
        >> Matrix double: 1.5
        >> SparceMatrix int: 4

        >> SparceMatrix int: 1
        >> SparceMatrix int: 4

    Как сделать поведение более предсказуемым? Ввести в область видимости функции Matrix с использованием using

    ```c++
        struct Matrix{
        virtual void pow(double x) { cout << "Matrix double: " << x << endl; }
        virtual void pow(int x) { cout << "Matrix int: " << x << endl; }
    };

    struct SparceMatrix: Matrix{
        using Matrix::pow;
        void pow(int x) { cout << "SparceMatrix int: " << x << endl; }
    };

    int main(){
        Matrix* m = new SparceMatrix;
        m->pow(1.5);
        m->pow(4);
        SparceMatrix sm;
        sm.pow(1.5);
        sm.pow(4);
    }
    ```
        >> Matrix double: 1.5
        >> SparceMatrix int: 4

        >> Matrix double: 1.5
        >> SparceMatrix int: 4
    > res: лекция 1, слайд 38

    