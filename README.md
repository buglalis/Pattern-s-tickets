
<!-- Картинка -->
<p align="center">
<img src="https://adonius.club/uploads/posts/2022-05/1653782388_69-adonius-club-p-kot-programmist-krasivo-foto-74.jpg" width="5000px"/>
</p>

Этот репозиторий создан для подготовки к зачету по предмету: "Паттерны проектирования, продвинутое программирование C++". Вот [тут](https://htmlacademy.ru/blog/html/markdown) ты можешь краткий гайд по markdown [(и ещё один побольше)](https://paulradzkov.com/2014/markdown_cheatsheet/)

## Список тем
* [Cmake](#Cmake)
* [Git](#Git)
* [Class](#Class)
* [Перегрузки и переопределения](# 'Перегрузкии переопределения')
* [goto](#Goto)
* [SFINAE](#SFINAE)


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

# Git
* ## 4 билет
    (Отличие checkout от switch.)
    
    git checkout - старая команда, для работы с ветками и файлами
    git switch - новая команда, для работы ТОЛЬКО с ветками. Для работы с файлами теперь тоже новая команда - restore

    ```bash
    git checkout -b <Название новой ветки>             # Создание новой ветки и переключение на неё
    git switch -с <Название новой ветки>               # Создание новой ветки и переключение на неё

    git checkout <Название ветки>                      # Переключение на существующую ветку
    git switch <Название ветки>                        # Переключение на существующую ветку

    git checkout <другая_ветка> -- <имя файла>         # Отменить изменения в рабочей директории для файла и установить версию из последнего коммита в другой 
                                                       # ветке (другую ветку можно не указывать, тогда будет взят последний коммит в текущей ветке)
    git switch -- file                                 # ОШИБКА

    git restore -s <другая ветка> file                 # новый способ откатить файл (здесь s = sorce,  а в качестве другой ветки можно взять, например,
                                                       # указатель на комит, предпредыдущий, тому, на который указывает master: master~2)
    ```

* ## 5 билет
    (Отличия rebase/merge/cherry-pick.)

    1. git rebase devel - собачка на молнии - "сшивает" коммиты по дате их создания (ветка devel "растворяется" в основной ветке)
    <img src="https://habrastorage.org/files/518/b8d/bce/518b8dbce1cd4f96b30de9782ae38fcd.png"/>
    2. git git merge devel - пожарная лестница, все коммиты ветки devel крепятся в конец, образуется пересечение (devel остается отдельной веткой, к которой можно вернуться)
    <img src="https://habrastorage.org/files/1ba/818/6d8/1ba8186d879d46ff85ea7c1e192328e2.png"/>
    3. git chery-pick idea - забрать коммиты из ветки idea
    <img src="https://habrastorage.org/files/271/7e3/091/2717e3091f644ef2954aa2de4514f446.png"/>


    P.S.
    1. git rebase master - ты синхронизируешься с главной веткой в которую коммитят все разработчики проекта, это полезно когда кто-то изменил участок кода с которым ты сейчас работаешь в своей ветке, дабы через неделю ты смог без проблем смержиться с master веткой. Обычно делается каждое утро перед началом рабочего дня и в конце когда фича готова.
    2.  git merge - обычно используется когда у вас 2 и более master ветки (к примеру master и prototype) в этих ветках очень много комитов (и rebase здесь не подходит) и обчно через пару недель, maintainer репозитория наработки из prototype ветки "сливает" в master ветку по средствам этого самого git merge.
    3. git chery-pick - ты забираешь комиты из одной ветки в другую, это бывает полезно когда изменения сделаные другим разработчиком в его ветке, прямо сейчас нужны тебе в твоей ветке, и что бы не писать этот код заново, ты забираешь его комит себе в ветку.



* ## 6 билет
    (Что нужно сделать, чтобы зафиксировать изменения в локальном репозитории и добавить их в удаленный репозиторий)

    * git status - проверка состояния репозитория (что модифицировано, что отслеживается, что ещё нет и т.п.)
    * git add <файл> - добавление файлов в индекс (становятся отслеживаемыми).
    * git rm <файл> - удаление файлов из индекса.
    * git commit -m <сообщение коммита> - фиксации отслеживаемых (проиндексированных) изменений в локальном репозитории.
    * git push - добавление изменений (зафиксированных коммитов) в удалённый репозиторий.
    * git pull - получение актуальных коммитов из удалённого репозитория. 

# Class
* ## 9 билет
    (Инварианты класса. Определение)

    **ОПР.** Утверждение, которое должно быть верно все время жизни объекта класса называется его **инвариантом**

    Инварианты класса необходимы для упрощение и удешевления его методов, так как им не нужно будет постоянно проверять их.
    #пример: List<>: 0 ≤ _size ≤ _items.Length 

    В С++ есть ряд язоковых средств, позволяющих сохранять инварианты классов:
    * механизм инкапсуляции (модификаторы досупа)
    * ссылки

* ## 10 билет
    (Инкапсуляция. Определение)

    **ОПР.** **Инкапсуляция** - один из принципов ООП. Её идея заключается в том, чтобы объединить данные и методы, работающие с этими данными в одном модуле, при этом, изолировав и сокрыв их от других

    Для инкапсуляции в С++ существуют модификаторы доступа к данным и методам класса (полям): [private, protected, public].

* ## 11 билет
    (Модификатор доступа в классе по умолчанию. Модификатор доступа в структуре по умолчанию.)

    * **Class**: private
    * **Struct**: public

* ## 12 билет
    (Наследование в классе по умолчанию. Наследование в структуре поумолчанию.)

    * **Class**: наследуется как private
    * **Struct**: наследуется как public

* ## 13 билет
    (Отличия private/protected/public (внутри определения класса и при наследовании).)

    1. Внутри определения класса:
    * **private**: элемент не доступен никому кроме -методов, реализованных в этом классе - методов дружественных классов -дружественных функций.
    * **protected**: элемент доступен только -методам этого класса -методам класса наследника (но не экземпляру) - друзьям.
    * **public**: элемент доступен всем методам и функциям в программе. Исключение составляет случай, когда клас унаследован как private. Тогда даже public-элементы этого класса будут недоступны в унаследованных классах.

    *P.S. friend - это плохо!*

    [Подробнее c рисуночками](https://www.bestprog.net/ru/2020/11/08/c-access-modifiers-private-protected-public-encapsulating-data-in-a-class-ru/)

    2. При наследовании (A - родитель B; C - родитель A):
    * **private**: private поля A недосутпны в классе B; public и protected поля A доступны в классе (но не из экзмепляров) B; поля A недоступны С.
    <img src="https://www.bestprog.net/wp-content/uploads/2020/11/05_02_02_08_09_02_01r-274x300.jpg"/>
    <img src="https://www.bestprog.net/wp-content/uploads/2020/11/05_02_02_08_09_02_02r-768x353.jpg">
    * **protected**: Модификатор доступа protected ограничивает доступ из экземпляров унаследованных классов и не ограничивает доступ из методов унаследованных классов.
    * **public**: protected и public A доступны из методов B; private A недоступны из методов B; public A доступны из экземпляров B; private и protected A недоступны из экземпляров B.
    <img src="https://www.bestprog.net/wp-content/uploads/2020/11/05_02_02_08_09_02_05r.jpg"/>
    
    [Подробнее c рисуночками](https://www.bestprog.net/ru/2020/11/09/c-using-private-protected-public-access-modifiers-when-inheriting-classes-ru/)


* ## 14 билет
    (Отличие malloc/free от new/delet.)

    <table class="s-table">
    <thead>
    <tr>
    <th><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Особенность</font></font></th>
    <th><code>new</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">/</font></font><code>delete</code></th>
    <th><code>malloc</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">/</font></font><code>free</code></th>
    </tr>
    </thead>
    <tbody>
    <tr>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Память, выделенная из</font></font></td>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">«Бесплатный магазин»</font></font></td>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">«Куча»</font></font></td>
    </tr>
    <tr>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Возвращает</font></font></td>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Полностью типизированный указатель</font></font></td>
    <td><code>void*</code></td>
    </tr>
    <tr>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">При неудаче</font></font></td>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Выбрасывает (никогда не возвращается </font></font><code>NULL</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">)</font></font></td>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Возвращает</font></font><code>NULL</code></td>
    </tr>
    <tr>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Требуемый размер</font></font></td>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Рассчитывается компилятором</font></font></td>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Должен быть указан в байтах</font></font></td>
    </tr>
    <tr>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Обработка массивов</font></font></td>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Имеет явную версию</font></font></td>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Требует ручных расчетов</font></font></td>
    </tr>
    <tr>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Перераспределение</font></font></td>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Не обрабатывается интуитивно</font></font></td>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Простой (без конструктора копирования)</font></font></td>
    </tr>
    <tr>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Вызов обратного</font></font></td>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Реализация определена</font></font></td>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Нет</font></font></td>
    </tr>
    <tr>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Случаи нехватки памяти</font></font></td>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Можно добавить новый распределитель памяти</font></font></td>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Не обрабатывается кодом пользователя</font></font></td>
    </tr>
    <tr>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Переопределяемый</font></font></td>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Да</font></font></td>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Нет</font></font></td>
    </tr>
    <tr>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Использование конструктора/деструктора</font></font></td>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Да</font></font></td>
    <td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Нет</font></font></td>
    </tr>
    </tbody>
    </table>
    
    Технически память, выделенная с помощью, newпоступает из «Free Store», а память, выделенная с помощью, mallocпоступает из «кучи». То, являются ли эти две области одинаковыми, является деталью реализации, что является еще одной причиной, по которой mallocих new **НЕЛЬЗЯ СМЕШИВАТЬ**.

# Перегрузки и переопределения

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

# goto

* ## 33 билет

	(Чем плохо goto?)

	goto ужасен, потому что:
	- можно вернуться назад или перескочить в другую область видимости
	- делает код запутанным и нечитабельным
	- можно проскочить обьявление переменной

* ## 34 билет

	(Примеры goto-маскирующих конструкций)

	- ```break```
	- ```continue```
	- ```switch```
	- ```return```
	- и т.п.

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
    Вызов p-> эквивалентен (p.operator->())->x и так сколько угодно раз. То есть стрелка "зарывается" в глубину на сколько может. Это и называется dill down behavior. Благодаря такому поведению всё, что можно получить - конкретное поле класса.
    ```c++
    struct A{
        int n;
        A* operator->() { std::cout << "A::operator->()" << endl;  return this; }
    };

    struct B{
            A* a;
            A* operator->() { std::cout << "B::operator->()" << endl; return  a;}
    };

    int main(){
            A a_obj {5};
            A* a_ptr = new A{2};
            B b_obj {a_ptr};

            cout << a_obj->n << endl;
            cout << b_obj->n << endl;
    }

    ```
        >> A::operator->()  
        >> 5  
        >> B::operator->()
        >> 2   

    > res: лекция 2, слайд 19

* ## 38 билет
    (Lvalue и rvalue. Отличия.)  
    lvalue представляет собой объект, который занимает идентифицируемое место в памяти (например, имеет адрес). Другими словами, некое выражение, ссылающееся на область памяти.

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
    > res: лекция 2, слайд 22;  
    > https://habr.com/ru/articles/348198/

* ## 39 билет
    (Rvalue ссылки.)  
    В C++11, если X – это тип, то X& – это lvalue reference, а X&& – это rvalue reference для этого типа. При этом важно понимать, что X&, X&& – это разные типы. С одной стороны, семантически они похожи, и о них можно говорить как о "ссылках вообще".  Тем не менее, поскольку это разные типы, для них работает перегрузка:
    ```c++
    int a, b;
    foo(int &x);
    foo(int &&x);
    foo(a * b); /* вызовет foo(int &&x) */
    foo(a); /* вызовет foo(int &x) */
    ```
    Ссылки Rvalue поддерживают реализацию семантики перемещения, что может значительно повысить производительность приложений. Семантика перемещения позволяет создавать код, который переносит ресурсы (например, динамически выделяемую память) из одного объекта в другой. Семантика перемещения работает, так как она позволяет передавать ресурсы из временных объектов: те, на которые нельзя ссылаться в других местах программы.
    > res: лекция 2, слайд 23;  
    > https://learn.microsoft.com/ru-ru/cpp/cpp/rvalue-reference-declarator-amp-amp?view=msvc-170&viewFallbackFrom=vs-2019;
    > https://www.rsdn.org/article/cpp/Cpp11NewStd.xml;  
    > RVO и NRVO: https://habr.com/ru/companies/vk/articles/666330/

* ## 40 билет
    (Что делает std::move?)  
    Функция move сама по себе не выполняет никаких перемещений, несмотря на название, а делает все возможное чтобы в данном конкретном примере вызвать конструктор перемещения - Vector(Vector&& rhs). То есть она из любого аргумента std::move делает rvalue. Эта функция в удовлетворяющем стандарту (20.2.3) виде может быть определена так:
    ```c++
    template<class T> 
    T&& std::move(T& a) noexcept
    {  
        returnstatic_cast<T&&>(a);
    }
    ```
    Пример её использования в реализации библиотечной функции std::swap из C++11
    ```c++
    template<class T> 
    void swap(T& a, T& b) 
    { 
        T tmp(std::move(a)); /* в tmp перемещается значение a, это гораздо дешевле */
        a = std::move(b); 
        b = std::move(tmp);
  } 
    ```
    Идея в том, что теперь обмен значениями может быть семантически выражен как обмен значениями, а не копирование (а значит, и оптимизирован соответствующим образом). Rvalue references позволяют (совместно с std::move) по-новому взглянуть на ссылки. Получается более тонко различать такие термины естественного языка, как "копирование", "присваивание значения", "присваивание результата" на языке C++. Это в свою очередь открывает как простор к оптимизациям, так и возможности более аккуратного выражения старых идиом. Хороший пример правильного использования rvalue references – это std::unique_ptr. Умение уместно использовать rvalue references – важная черта, отличающая программиста на C++11.

    **Доп инфа: RVO**  
    Но кроме std::move, для оптимизации используется ещё RVO и NRVO принципы. RVO в основном означает, что компилятору разрешено избегать создания временных объектов для возвращаемых значений, даже если они имеют побочные эффекты.
    ```c++
    Snitch ExampleRVO() {
        return Snitch();
    }

    int main() {
        Snitch snitch = ExampleRVO();
    }
    ```
    С использованием RVO:  

        >> $ clang++ -std=c++11 main.cpp && ./a.out  
        >> c'tor  
        >> d'tor  
    Без него (ключ -fno-elide-constructors его отключает)  

        >> $ clang++ -std=c++11 -fno-elide-constructors main.cpp && ./a.out  
        >> c'tor  
        >> move c'tor  
        >> d'tor  
        >> move c'tor  
        >> d'tor  
        >> d'tor  


    > res: лекция 2, слайд 33;  
    > https://habr.com/ru/articles/587644/;  
    > https://stackoverflow.com/questions/3413470/what-is-stdmove-and-when-should-it-be-used-and-does-it-actually-move-anythi;  
    > https://shaharmike.com/cpp/rvo/

* ## 41 билет
    (Правило нуля, трех, пяти.)  
    **Правило трёх** - правило в С++, гласящее, что если класс или структура определяет один из следующих методов, то они должны явным образом определить все три метода: Деструктор, Конструктор копирования, Оператор присваивания копированием.    
      
    С выходом одиннадцатого стандарта правило расширилось и стало называться **правилом пяти**. Теперь при реализации конструктора необходимо реализовать: Деструктор, Конструктор копирования, Оператор присваивания копированием, Конструктор перемещения, Оператор присваивания перемещением.  
      
    **Правило нуля** - если ничего из специальных функций-членов не определено пользователем, то (с учетом переменных-членов) компилятор предоставит реализации по умолчанию для каждой из них. Правило Ноля заключается в том, что тот сценарий, когда не нужно определять ничего из специальных функций-членов, должен быть предпочтительным.
    > res: лекция 2, слайд 34

* ## 46 билет
	(ODR) 

	У каждой сущности в С++ есть обьявление (declaration) и определение (definition).

	Declaration  | Definition
	------------- | -------------
	class X;  | class X { int foo(); };
	int bar();  | int bar() { return 42; }
	extern int x; | int x = 10;

	[**ODR**](https://ru.wikipedia.org/wiki/%D0%9F%D1%80%D0%B0%D0%B2%D0%B8%D0%BB%D0%BE_%D0%BE%D0%B4%D0%BD%D0%BE%D0%B3%D0%BE_%D0%BE%D0%BF%D1%80%D0%B5%D0%B4%D0%B5%D0%BB%D0%B5%D0%BD%D0%B8%D1%8F) - _one definition rule_ - сколько угодно обьявлений, но не более одного определения.

	Однако шаблоны нарушают ODR, т.к. для каждого типа, с которым была вызвана функция, компилятор генерируетс новое определение:
	``` c++
	//// foo.h

	template <typename T> T foo(T x) {  /* ... */  }
	```
	``` c++
	//// module1.cpp

	// int foo(int x);

	foo<int>(1);

	// int foo(int x) { /* ... */ }
	```
	``` c++
	//// module2.cpp

	// double foo(double x);

	foo<double>(2.);

	// double foo(double x) { /* ... */ }
	```
	Для шаблонов исключение в ODR, они работают.
	> 3-я лекция, 16-17 слайды

* ## 47 билет
	(Include guards)

	[**Include guards**](https://ru.wikipedia.org/wiki/Include_guard) - защита от повторного подключения файла при использовании ```#include```. Использование их гарантирует, что при повторных подключениях, файл не будет подключаться.
	``` c++
	//// foo.h

	#ifndef FOO_H
	#define FOO_H

	int foo();

	#endif
	```
	Аналог include guards - ```#pragma once```. В стандарте ее нету, но она поддерживается почти всеми компиляторами. Только стоит быть осторожным с ней, т.к. задача определения идентичности файла по указанному пути, в общем случае, довольно трудная, и нет гарантий, что в компиляторе она хорошо решена.
	``` c++
	//// foo.h

	#pragma once

	int foo();
	```
* ## 48 билет
    (CV-квалификаторы при выводе типов)
    
    [**CV-квалификаторы**](https://bestprogrammer.ru/programmirovanie-i-razrabotka/kvalifikatory-c-i-spetsifikatory-klassov-hraneniya) (const and volatile qualifiers):
    
    - ```const``` - запрещает менять значение переменной
    - ```volatile``` - запрещает компилятору оптимизировать переменную ([подробнее](https://habr.com/ru/articles/673428/))

    **Вывод типов** - возможность компилятора самостоятельно определить тип выражения.
    ``` c++
    int x = std::max(1, 2);  // вызовет std::max<int> (int, int)

    int y = std::max(1, 2.); // error: 'int' vs 'double'
    ```
    При выводе типов внешние cv-квалификаторы и ссылки режутся:
    ``` c++
    const int& a = 1;
    const int& b = 2;
    std::max(a, b);          // вызовет std::max<int> (int, int)
    ```
    Если шаблон был уточнен, то cv-квалификаторы сохраняются:
    ``` c++
    template<typename T> void foo(const T& x);
    
    const int& a = 1;
    foo(a);                  // вызовет foo<int> (const int&)
    ```
	> 3-я лекция, 19-20 слайды
* ## 49 билет
    (Манглирование имен. (Как перегрузки функций представляются компилятором))
    
    В С++ можно перегружать функции. Но в скомпилированном коде нету информации о типе данных аргументов. Чтобы различать перегруженные функции, компилятор выполняет **манглирование**: каким-то образом преобразует имя функции под каждую перегрузку. Например, он может дописывать к имени размер аргументов в байтах. В стандарте про манглирование ничего не написано, поэтому каждый компилятор решает эту проблему как хочет.
    
* ## 50 билет
    (Правила разрешения перегрузки для нешаблонных функций)
    
    - **Идеальное совпадение выигрывает**
        - Правильный тип 
        ``` c++
        int foo(const int&); // 1
        int foo(short);      // 2
        
        foo(1);              // вызовет 1
        ```
        ``` c++
        int foo(const int&); // 1
        int foo(int&);       // 2
        
        foo(1);              // вызовет 1
        int y;
        foo(y);              // вызовет 2
        ```
        - Если идеальных совпадений несколько - конфликт
        ``` c++
        int foo(const int&); // 1
        int foo(int);        // 2
        
        foo(1);              // ERROR
        ```
    - **Все стандартные преобразования равны**
        - Все стандартные преобразования одноранговые
        ``` c++
        int foo(long long);  // 1
        int foo(char);       // 2
        
        foo(1);              // ERROR
                             // компилятор не знает, к какому типу приводить int
        ```
        - Любые стандартные преобразования выигрывают у пользовательских
        ``` c++
        struct MyClass { 
            MyClass (int);
        };
        
        int foo(char);       // 1
        int foo(MyClass);    // 2
        
        foo(1);              // вызовет 1
        ```
    - **[Многоточия](https://radioprog.ru/post/1202) проигрывают почти всему** 
        - Проигрывают и стандартным, и пользовательским преобразованиям
        ``` c++
        void foo(int);           // 1
        void foo(char);          // 2
        void foo(MyClass);       // 3
        void foo(...);           // 4

        foo(1);                  // Приоритет вызова функций от большего к меньшому: 1, 2, 3, 4
                                 // То есть, если есть 1, то вызовет 1, если нет, то 2 и т.д.
        ```
        - Выигрывают только у неправильных ссылок
        ``` c++
        void foo(...);           // 1
        void foo(int&);          // 2
        
        foo(1);                  // вызовет 1
        ```
	> 3-я лекция, 27-30 слайды

    **Ограничения при перегрузке**:
    - Функция не может быть перегружена cv-квалификатором:
    ``` c++
    void f(int x);
    void f(const int x);         // ERROR
    
    void f(char* x);
    void f(char* const x);       // ERROR 
    ```
    
    - К cv-квалификаторам внешнего типа (квалификаторы, ограничивающие изменение внешней переменной), это не относится:
    ``` c++
    void f(int& x);
    void f(const int& x);        // OK
    
    void f(char* x);
    void f(const char* x);       // OK 
    ```
    > 3-я лекция, 26-ой слайд

* ## 51 билет
    (Правила разрешения перегрузки для шаблонных функций)
    
    - Точно подходящая функция выигрывает у шаблона
        - Даже если шабло подходит идеально
        ``` c++
        void foo(int);                     // 1
        template<typename T> void foo(T);  // 2
        
        foo(1);                            // вызовет 1
        ```
        - Можем попросить вызвать шаблон
        ``` c++
        foo<>(1);                          // вызовет 2
        foo<int>(1);                       // вызовет 2
        ```
        - Стандартные преобразования проигрывают шаблону
        ``` c++
        foo(1.);                           // вызовет 2
        ```
    - Более специальный шаблон выигрывает у менее специального
    ``` c++
    template<typename T> void foo(T);      // 1
    template<typename T> void foo(T*);     // 2
    template<typename T> void foo(T**);    // 3
    template<typename T> void foo(T***);   // 4
    
    int*** a;
    foo(a);                                // вызовет 4
    foo<int**>(a);                         // вызовет 2
                                           // т.к. T = int**, type(a) = int*** = T*
    ```
    - Меньшее количество аргументов выигрывает против большего
    ``` c++
    template<typename T1, typename T2> foo(T1, T2); // 1
    template<typename T> foo(T, T*);                // 2
    
    int x, y;
    foo(x, &y);                                     // вызовет 2
    ```
    > 3-я лекция, 32-38 слайды

# SFINAE

* ## 58 билет
    (SFINAE. Определение)
    
    **SFINAE** - _Substitution Failure Is Not An Error_ - провал подстановки не является ошибкой
    ``` c++
    template<typename T> T max(T, T);                   // 1
    template<typename T1, template T2> T max(T1, T2);   // 2
    
    auto max(1, 2.);                                    // подстановка в 1 провалена
                                                        // подстановка в 2 успешна
                                                        // код работает!
    ```
    > 6-ая лекция, начиная с 4-ого слайда

* ## 59 билет
    (Зачем в старом С++ (до С++11) для реализации SFINAE создавались структуры разных размеров?)
    
    Структуры разных размеров нужны, чтобы на этапе компиляции определить, какая из перегруженных функций была вызвана. Например, мы хотим определить, есть ли у класса метод sort()
    ``` c++
    // создаем структуры разных размеров
    struct yes { char c1; };            // sort() есть
    struct no { char c1; char c2; };    // sort() нет
    
    template <typename T> 
    yes have_sort (decltype(&T::sort)); // в качестве аргумента подаем тип данных метода sort()
    
    template <typename T> 
    no have_sort (...);                 // многоточие вызывается в самом последнем случае,
                                        // то есть если предыдущая подстановка не удалась,
                                        // а она не удастся только если нет метода sort()
                    
    template <typename T>
    void check_sort(T x){
        if ( sizeof( have_sort<T>(NULL) ) == sizeof( yes ) ){   // размер возвращаемого типа равен yes,
                                                                // значит первая подстановка выполнена
                                                                // успешно, а следовательно sort() есть
            std::cout << "have sort()" << std::endl;
        } else {
            std::cout << "haven't sort()" << std::endl;
        }
    }
    ```
    > аналог структурам have_sort/have_range/have_nothing из 6-ой лекции, 29-30 слайды

* ## 60 билет
    (Замена структурам разных размеров в современном С++)
    
    В C++11 был добавлен класс [_std::integral_constant_](https://en.cppreference.com/w/cpp/types/integral_constant). А также два его typedef'a: ```std::true_type``` и ```std::false_type```. И теперь мы можем избавиться от лишних структур:
    ``` c++
    template <typename T> 
    std::true_type have_sort (decltype(&T::sort));
    
    template <typename T> 
    std::false_type have_sort (...);  
                    
    template <typename T>
    void check_sort(T x){
        if ( have_sort<T>(NULL).value ){        // у std::true_type value = true
            std::cout << "have sort()" << std::endl;
        } else {
            std::cout << "haven't sort()" << std::endl;
        }
    }
    ```
        
    Еще был добавлен [_std::enable_if_](https://learn.microsoft.com/ru-ru/cpp/standard-library/enable-if-class?view=msvc-170), что тоже может упростить реализацию SFINAE.
    ``` c++
    bool flag;
    
    template <typename T>
    std::enable_if<flag, T> f(T x);        
    // если flag == true, то std::enable_if<flag, T> эквивалентен T
    // если false, то происходит ошибка подстановки
    ```
    > был применен в 6-ой лекции, 32-ой слайд
