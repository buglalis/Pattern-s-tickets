
<!-- Картинка -->
<p align="center">
<img src="https://adonius.club/uploads/posts/2022-05/1653782388_69-adonius-club-p-kot-programmist-krasivo-foto-74.jpg" width="5000px"/>
</p>

Этот репозиторий создан для подготовки к зачету по предмету: "Паттерны проектирования, продвинутое программирование C++". Вот [тут](https://htmlacademy.ru/blog/html/markdown) ты можешь краткий гайд по markdown [(и ещё один побольше)](https://paulradzkov.com/2014/markdown_cheatsheet/)

## Список тем
* [Cmake](#Cmake)
* [Git](#Git)
* [Class](#Class)
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

# Git
* ## |4 билет| Git.
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

* ## |5 билет| Git.
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



* ## |6 билет| Git.
    (Что нужно сделать, чтобы зафиксировать изменения в локальном репозитории и добавить их в удаленный репозиторий)

    * git status - проверка состояния репозитория (что модифицировано, что отслеживается, что ещё нет и т.п.)
    * git add <файл> - добавление файлов в индекс (становятся отслеживаемыми).
    * git rm <файл> - удаление файлов из индекса.
    * git commit -m <сообщение коммита> - фиксации отслеживаемых (проиндексированных) изменений в локальном репозитории.
    * git push - добавление изменений (зафиксированных коммитов) в удалённый репозиторий.
    * git pull - получение актуальных коммитов из удалённого репозитория. 

# Class
* ## |9 билет| Class.
    (Инварианты класса. Определение)

    **ОПР.** Утверждение, которое должно быть верно все время жизни объекта класса называется его **инвариантом**

    Инварианты класса необходимы для упрощение и удешевления его методов, так как им не нужно будет постоянно проверять их.
    #пример: List<>: 0 ≤ _size ≤ _items.Length 

    В С++ есть ряд язоковых средств, позволяющих сохранять инварианты классов:
    * механизм инкапсуляции (модификаторы досупа)
    * ссылки

* ## |10 билет| Class.
    (Инкапсуляция. Определение)

    **ОПР.** **Инкапсуляция** - один из принципов ООП. Её идея заключается в том, чтобы объединить данные и методы, работающие с этими данными в одном модуле, при этом, изолировав и сокрыв их от других

    Для инкапсуляции в С++ существуют модификаторы доступа к данным и методам класса (полям): [private, protected, public].

* ## |11 билет| Class.
    (Модификатор доступа в классе по умолчанию. Модификатор доступа в структуре по умолчанию.)

    * **Class**: private
    * **Struct**: public

* ## |12 билет| Class.
    (Наследование в классе по умолчанию. Наследование в структуре поумолчанию.)

    * **Class**: наследуется как private
    * **Struct**: наследуется как public

* ## |13 билет| Class.
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


* ## |14 билет| Class.
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
