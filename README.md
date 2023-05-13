
<!-- Картинка -->
<p align="center">
<img src="https://adonius.club/uploads/posts/2022-05/1653782388_69-adonius-club-p-kot-programmist-krasivo-foto-74.jpg" width="5000px"/>
</p>

Этот репозиторий создан для подготовки к зачету по предмету: "Паттерны проектирования, продвинутое программирование C++". Вот [тут](https://htmlacademy.ru/blog/html/markdown) ты можешь краткий гайд по markdown [(и ещё один побольше)](https://paulradzkov.com/2014/markdown_cheatsheet/)

## Список тем
* [Cmake](#Cmake)
* [Классы](#Классы)


# Cmake
* ## 1 билет
    (Написать Cmake для проекта, состоящего из main.cpp, class1.h, class1.cpp. Стандарт С++ - 17.)
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
    
* ## 2 билет
    (Алгоритм сборки библиотеки без зависимостей из исходных кодов, которые содержат CmakeLists.txt.)

    Пусть есть проект библиотеки в папке **~/my_lib** и мы хотим собрать ее по адресу **~/app**, тогда алгоритм сборки будет такой:
    ```bash
    cd ~/my_lib 
    mkdir build && cd build 
    cmake .. -DCMAKE_INSTALL_PREFIX=<директория установки>
    cmake --build . --target install -- -j<кол-во потоков>
    ```

* ## 3 билет
    (Добавление сторонней прекомпилированной библиотеки к своему проекту)

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
    (Использование аргументов со значениями по умолчанию в виртуальных функциях.)

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
    (NVI. Пример.)

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
    (Может ли существовать шаблон виртуального метода?)
    Нет. 
    > res: лекция 1, слайд 37

* ## 31 билет
    (Можно ли перегружать виртуальные функции?)
    Да.
    > res: лекция 1, слайд 37
* ## 32 билет
    (Как добавить в overloading set класса-наследника методы базового класса являющиеся перегрузками виртуального метода?)
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

    Сделать доступной перегрузку pow для double можно, если ввести в область видимости функции Matrix с использованием using

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
    > res: лекция 1, слайд 38;  
    > https://radioprog.ru/post/1274;  
    > https://radioprog.ru/post/1275

* ## 35 билет
    (RAII. Концепция.)
    RAII - resource acqupation is initialization. Это программная идиома, смысл которой заключается в том, что с помощью тех или иных программных механизмов получение некоторого ресурса неразрывно совмещается с инициализацией, а освобождение — с уничтожением объекта. Типичным (хотя и не единственным) способом реализации является организация получения доступа к ресурсу в конструкторе, а освобождения — в деструкторе соответствующего класса.
    > res: лекция 2, слайд 9

* ## 36 билет
    (Инвариант умного указателя с единственным(уникальным) владением.)
    "Никто, кроме меня, не имеет доступ к указателю, который я храню."  
    Пример такого класса
    ```c++
        template<typename T> class ScopedPointer{
            T* ptr;
        public:
            ScopedPointer(T* ptr_ = nullptr): ptr(ptr_) {};
            ~ScopedPointer() { delete ptr; }
            T& operator*() { return *ptr; }
            const T& operarot*() const { return *ptr; }
            T* operator->() { return ptr; }
            const T* operator->() const { return ptr; }
        }
    ```
    > res: лекция 2, слайд 11

* ## 37 билет
    (Drill down behaviour. Где применяется?)
    Вызов p-> эквивалентен (p.operator->())->x и так сколько угодно раз. То есть стрелка "зарывается" в глубину на сколько может. Это и называется dill down behavior. Благодаря такому поведению можно получить конкретное поле класса.

    > res: лекция 2, слайд 19

* ## 38 билет
    (Lvalue и rvalue. Отличия.)
    lvalue представляет собой объект, который занимает идентифицируемое место в памяти (например, имеет адрес).

    rvalue — это не lvalue (любое выражение является либо lvalue, либо rvalue). Таким образом из определения lvalue следует, что rvalue — это выражение, которое не представляет собой объект, который занимает идентифицируемое место в памяти.

    Пример
    ```c++
    int var;
    var = 4;
    ```
    Оператор присваивания ожидает lvalue с левой стороны, и var является lvalue, потому что это объект с идентифицируемым местом в памяти.
    ```c++
    4 = var;       // ERROR!
    (var + 1) = 4; // ERROR!
    ```
    Ни константа 4, ни выражение var + 1 не являются lvalue
    (что автоматически их делает rvalue). Они не lvalue, потому что оба являются временным результатом выражений, которые не имеют определённого места в памяти (то есть они могут находится в каких-нибудь временных регистрах на время вычислений). Таким образом, присваивание в данном случае не несёт в себе никакого семантического смысла. Иными словами — некуда присваивать.
    > res: лекция 2, слайд 22;  
    > https://habr.com/ru/articles/348198/

* ## 39 билет
    (Rvalue ссылки.)  
    В C++11, если X – это тип, то X& – это lvalue reference, а X&& – это rvalue reference для этого типа (8.3.2.2). При этом важно понимать, что X&, X&& – это разные типы. С одной стороны, семантически они похожи, и о них можно говорить как о "ссылках вообще".  
    **Доп инфа: преобразвания между lvalue и rvalue.**  
    Преобразование возможно лишь в одну сторону: lvalue->rvalue. Обратное бы нарушило суть lvalue согласно определению.
    ```c++
    int a = 1;        // a - lvalue
    int b = 2;        // b - lvalue
    int c = a + b;    // '+' требует rvalue, поэтому a и b конвертируются в rvalue 
                      // и rvalue возвращается в качестве результата
    ```
    Это не означает, что lvalue не могут быть получены из rvalue явным способом. Например, унарный оператор '*' (разыменование) принимает rvalue в качестве аргумента, но возвращает lvalue в качестве результата. 
    ```c++
        int arr[] = {1, 2};
        int* p = &arr[0];
        *(p + 1) = 10;   // OK: p + 1 rvalue, однако *(p + 1) уже lvalue
    ```
    Обратно, унарный оператор '&' (адрес) принимает lvalue как аргумент и производит rvalue.
    ```c++
        int var = 10;
        int* bad_addr = &(var + 1);  // ОШИБКА: требуется lvalue для унарного оператора '&'
        int* addr = &var;            // ОК: var - lvalue
        &var = 40;                   // ОШИБКА: требуется lvalue с левой стороны
                                    //         оператора присваивания
    ```
    > res: лекция 2, слайд 23;
    > https://habr.com/ru/articles/348198/;
    > https://www.rsdn.org/article/cpp/Cpp11NewStd.xml;

* ## 40 билет
    (Что делает std::move?)  
    функция move сама по себе не выполняет никаких перемещений, несмотря на название, а делает все возможное чтобы в данном конкретном примере вызвать конструктор перемещения - Vector(Vector&& rhs).
    res: https://habr.com/ru/articles/587644/;
    https://stackoverflow.com/questions/3413470/what-is-stdmove-and-when-should-it-be-used-and-does-it-actually-move-anythi

* ## 41 билет
    (Правило нуля, трех, пяти.)  
    **Правило трёх** - правило в С++, гласящее, что если класс или структура определяет один из следующих методов, то они должны явным образом определить все три метода: value для унарного Деструктор * Конструктор копирования Оператор присваивания копированием value с левой стороны исваивания.  
    С выходом одиннадцатого стандарта правило расширилось и стало называться **правилом пяти**. Теперь при реализации конструктора необходимо реализовать: Деструктор Конструктор копирования *Оператор присваивания копированием ений, несмотря на тном примере вызвать • Конструктор перемещения * Оператор присваивания перемещением. **Правило нуля** - если ничего из специальных функций-членов не определено пользователем, то (с учетом переменных-членов) компилятор предоставит реализации по умолчанию для каждой из них. Правило Ноля заключается в том, что тот сценарий, когда не нужно определять ничего из специальных функций-членов, должен быть предпочтительным.