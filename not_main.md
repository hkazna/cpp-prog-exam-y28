## 11. Наследование. Виртуальные функции. Виртуальное наследование. Множественное наследование. Организации взаимодействия между классами. ##
Наследование (inheritance) представляет один из ключевых аспектов объектно-ориентированного программирования, который позволяет наследовать функциональность одного класса (базового класса) в другом - производном классе (derived class).

Зачем нужно наследование? Рассмотрим небольшую ситуацию, допустим, у нас есть классы, которые представляют человека и сотрудника компании:
```cpp
#include <iostream>

class Person {
public:
    std::string _name;
    int _age;

    void display() const {
        std::cout << "Name: " << _name << " age: " << _age << std::endl;
    }
};

class Employee {
    public:
    std::string _name;
    int _age;
    std::string _company_name;

    void display() const {
        std::cout << "Name: " << _name << " age: " << _age << std::endl;
    }
};
```
В данном случае класс Employee фактически содержит функционал класса Person: свойства name и age и функцию display(). В целях демонстрации все переменные здесь определены как публичные. И здесь, с одной стороны, мы сталкиваемся с повторением функционала в двух классах. С другой строны, мы также сталкиваемся с отношением is ("является"). То есть мы можем сказать, что сотрудник компании ЯВЛЯЕТСЯ человеком. Так как сотрудник компании имеет в принципе все те же признаки, что и человек (имя, возраст), а также добавляет какие-то свои (компанию). Поэтому в этом случае лучше использовать механизм наследования. Унаследуем класс Employee от класса Person:
```cpp
#include <iostream>

class Person {
public:
    std::string _name;
    int _age;

    void display() const {
        std::cout << "Name: " << _name << "\tAge: " << _age << std::endl;
    }
};

class Employee : public Person {
public:
    std::string _company_name;
};
```

Для установки отношения наследования после названия класса ставится двоеточие, затем идет спецификатор доступа и название класса, от которого мы хотим унаследовать функциональность. В этом отношении класс Person еще будет называться базовым классом (также называют суперклассом, родительским классом), а Employee - производным классом (также называют подклассом, классом-наследником).

Спецификатор доступа позволяет указать, к каким членам класса производный класс будет иметь доступ. В данном случае используется спецификатор public:
```
public:
    std::string _name;       //  имя
    int _age;           // возраст
    void display() const {
        std::cout << "Name: " << _name << "\tAge: " << _age << std::endl;
    }
```

который позволяет использовать в производном классе все публичные члены базового класса. Если мы не используем модификатор доступа, то класс Employee ничего не будет знать о переменных name и age и функции print.

После установки наследования мы можем убрать из класса Employee те переменные, которые уже определены в классе Person. Используем оба класса:
```cpp
#include <iostream>

class Person {
public:
    std::string _name;
    int _age;

    void display() const {
        std::cout << "Name: " << _name << "\tAge: " << _age << std::endl;
    }
};

class Employee : public Person {
public:
    std::string _company_name;
};

int main() {
    Person Nikita;
    Nikita._name = "Nikita";
    Nikita._age = 21;
    Nikita.display(); // Name: Nikita      Age: 21

    Employee Emin;
    Emin._name = "Emin";
    Emin._age = 21;
    Emin._company_name = "Ozon";
    Emin.display(); // Name: Emin       Age: 21
}
```
Таким образом, через переменную класса Employee мы можем обращаться ко всем открытым членам класса Person.

Конструкторы
Но теперь сделаем все переменные приватными, а для их инициализации добавим конструкторы. И тут стоит учитывать, что конструкторы при наследовании не наследуются. И если базовый класс содержит только конструкторы с параметрами, то производный класс должен вызывать в своем конструкторе один из конструкторов базового класса:
```cpp
#include <iostream>

class Person {
    std::string _name;
    int _age;
public:
    Person(std::string name, int age) : _name(name), _age(age) {}

    void display() const {
        std::cout << "Name: " << _name << "\tAge: " << _age << std::endl;
    }
};

class Employee : public Person {
    std::string _company_name;
public:
    Employee(std::string name, int age, std::string company_name) : Person(name, age), _company_name(company_name) {}
};

int main() {
    Person Nikita("Nikita", 21);
    Nikita.display(); // Name: Nikita      Age: 21

    Employee Emin("Emin", 21, "Ozon");
    Emin.display(); // Name: Emin       Age: 21
}
```

Подключение конструктора базового класса
В примерах выше конструктор Employee отличается от конструктора Person одним параметром - company. Все остальные параметры из Employee передаются в Person. Однако, если бы у нас было бы полное соответствие по параметрам между двумя классами, то мы могли бы и не определять отдельный конструктор для Employee, а подключить конструктор базового класса:
```cpp
#include <iostream>

class Person {
    std::string _name;
    int _age;
public:
    Person(std::string name, int age) : _name(name), _age(age) {}

    void display() const {
        std::cout << "Name: " << _name << "\tAge: " << _age << std::endl;
    }
};

class Employee : public Person {
public:
    using Person::Person; // подключили конструктор базового класса
};

int main() {
    Person Nikita("Nikita", 21);
    Nikita.display(); // Name: Nikita      Age: 21

    Employee Emin("Emin", 21);
    Emin.display(); // Name: Emin       Age: 21
}
```

Здесь в классе Employee подключаем конструктор базового класса с помощью ключевого слова using:

``` using Person::Person; ```
Таким образом, класс Employee фактически будет иметь тот же конструктор, что и Person с теми же двумя параметрами. И этот конструктор мы также можем вызвать для создания объекта Employee:

``` Employee employee {"Ivan", 19}; ```

Определение конструкторов копирования
При определении конструктора копирования в производном классе следует вызывать в нем конструктор копирования базового класса. Например, добавим в классы Person и Employee конструкторы копирования:
```cpp
#include <iostream>

class Person {
    std::string _name;
    int _age;
public:
    //Конструктор копирования
    Person(const Person& person) {
        _name = person._name;
        _age = person._age;
    }

    Person(std::string name, int age) {
        this->_name = name;
        this->_age = age;
    }

    void display() const {
        std::cout << "Name: " << _name << "\tAge: " << _age << std::endl;
    }
};

class Employee : public Person {
    std::string _company_name;
public:
    Employee(std::string name, int age, std::string company_name) : Person(name, age) {
        this->_company_name = company_name;
    }

    // Конструктор копирования
    Employee(const Employee& employee) : Person(employee) {
        _company_name = employee._company_name;
    }
};

int main() {
    Person Nikita("Nikita", 21);
    Nikita.display(); // Name: Nikita      Age: 21

    Employee Emin("Emin", 21, "Ozon");
    Emin.display(); // Name: Emin       Age: 21
}
```

В конструкторе копирования производного класса Employee вызываем конструктор копирования базового класса Person
При этом в конструктор копирования Person передается объект employee, где будут установлены переменные name и age. В самом же конструкторе класса Employee лишь устанавливается переменная company.


Наследование деструкторов
Уничтожение объекта производного класса может вовлекать как собственно деструктор производного класса, так и деструктор базового класса. Например, определим в обоих классах деструкторы
```cpp
#include <iostream>

class Person {
    std::string _name;
    int _age;

public:
    Person(std::string name, int age) {
        this->_name = name;
        this->_age = age;

        std::cout << "Person created" << std::endl;
    }

    ~Person() {
        std::cout << "Person deleted" << std::endl;
    }

    void display() const {
        std::cout << "Name: " << _name << "\tAge: " << _age << std::endl;
    }
};

class Employee : public Person {
    std::string _company_name;

public:
    Employee(std::string name, int age, std::string company_name) : Person(name, age) {
        this->_company_name = company_name;

        std::cout << "Employee created" << std::endl;
    }

    ~Employee() {
        std::cout << "Employee deleted" << std::endl;
    }

};

int main() {
    Employee Emin("Emin", 21, "Ozon");
    Emin.display(); // Name: Emin       Age: 21
}
```

В обоих классах деструктор просто выводит некоторое сообщение. В функции main создается один объект Employee, однако при завершении программы будет вызываться деструктор как из производного, так и из базового класса

Консольный вывод:


![image](https://github.com/user-attachments/assets/88c1597a-0736-4132-80f5-51e924181389)


Также стоит отметить, что иногда наследование от класса может быть нежелательно. И с помощью спецификатора final мы можем запретить наследование:
```cpp
class Person final {
};
```

Тогда, если мы попробуем наследоваться от Person, то получим ошибку
```cpp
class Employee : public Person { // ошибка
};
```

ТАКЖЕ ВАЖНО ТАБЛИЦА НАСЛЕДОВАНИЯ  В С++

![image](https://github.com/user-attachments/assets/ee7001d9-d07a-4d39-a032-73632ef19a36)


### Виртуальные функции
При вызове функции программа должна определять, с какой именно реализацией функции соотносить этот вызов, то есть связать вызов функции с самой функцией. В С++ есть два типа связывания - статическое и динамическое.

Когда вызовы функций фиксируются до выполнения программы на этапе компиляции, это называется статическим связыванием (static binding), либо ранним связыванием (early binding). При этом вызов функции через указатель определяется исключительно типом указателя, а не объектом, на который он указывает. Например:
```cpp
#include <iostream>
  
class Person
{
public:
    Person(std::string name): name{name}
    { }
    void print() const
    {
        std::cout << "Name: " << name << std::endl;
    }
private:
    std::string name;       //  имя
};
class Employee: public Person
{
public:
    Employee(std::string name, std::string company): Person{name}, company{company}
    { }
    void print() const
    {
        Person::print();
        std::cout << "Works in " << company << std::endl;
    }
private:
    std::string company;    // компания
};
  
int main()
{
    Person tom {"Tom"};
    Person* person {&tom};
    person->print();     // Name: Tom
 
    Employee bob {"Bob", "Microsoft"};
    person = &bob;
    person->print();    // Name: Bob
}
```

В данном случае класс Employee наследуется от класса Person, но оба этих класса определяют функцию print(), которая выводит данные об объекте. В функции main создаем два объекта и поочередно присваиваем их указателю на тип Person и вызываем через этот указатель функцию print. Однако даже если этому указателю присваивается адрес объекта Employee, то все равно вызывает реализация функции из класса Person:
```cpp
Employee bob {"Bob", "Microsoft"};
person = &bob;
person->print();    // Name: Bob
```

То есть выбор реализации функции определяется не типом объекта, а типом указателя. 

Динамическое связывание и виртуальные функции
Другой тип связывания представляет динамическое связывание (dynamic binding), еще называют поздним связыванием (late binding), которое позволяет на этапе выполнения решать, функцию какого типа вызвать. Для этого в языке С++ применяют виртуальные функции. Для определения виртуальной функции в базовом классе функция определяется с ключевым словом virtual. Причем данное ключевое слово можно применить к функции, если она определена внутри класса. А производный класс может переопределить ее поведение.

Итак, сделаем функцию print в базовом классе Person виртуальной:
```cpp
#include <iostream>
  
class Person
{
public:
    Person(std::string name): name{name}
    { }
    virtual void print() const  // виртуальная функция
    {
        std::cout << "Name: " << name << std::endl;
    }
private:
    std::string name;
};
class Employee: public Person
{
public:
    Employee(std::string name, std::string company): Person{name}, company{company}
    { }
    void print() const
    {
        Person::print();
        std::cout << "Works in " << company << std::endl;
    }
private:
    std::string company;
};
  
int main()
{
    Person tom {"Tom"};
    Person* person {&tom};
    person->print();     // Name: Tom
    Employee bob {"Bob", "Microsoft"};
    person = &bob;
    person->print();    // Name: Bob
                            // Works in Microsoft
}
```
Таким образом, базовый класс Person определяет виртуальную функцию print, а производный класс Employee переопределяет ее. В первом же примере, где функция print не была виртуальной, класс Employee не переопределял, а скрывал ее. Теперь при вызове функции print для объекта Employee через указатель Person* будет вызываться реализация функции именно класса Employee.

В этом и состоит отличие переопределения виртуальных функций от скрытия.

Класс, который определяет или наследует виртуальную функцию, еще назвается полиморфным (polymorphic class). То есть в данном случае Person и Employee являются полиморфными классами.

Стоит отметить, что вызов виртуальной функции через имя объекта всегда разрешается статически.
```cpp
Employee bob {"Bob", "Microsoft"};
Person p = bob;
p.print();  // Name: Bob - статическое связывание
```
Динамическое связывание возможно только через указатель или ссылку.
```cpp
Employee bob {"Bob", "Microsoft"};
Person &p {bob};    // присвоение ссылке
p.print();  // динамическое связывание
 
Person *ptr {&bob};    // присвоение адреса указателю
ptr->print();  // динамическое связывание
```

При определении вирутальных функций есть ряд ограничений. Чтобы функция попадала под динамическое связывание, в производном классе она должна иметь тот же самый набор параметров и возвращаемый тип, что и в базовом классе. Например, если в базовом классе виртуальная функция определена как константная, то в производном классе она тоже должна быть константной. Если же функция имеет разный набор параметров или несоответствие по константности, то мы будем иметь дело со скрытием функций, а не переопределением. И тогда будет применяться статическое связывание.

Также статические функции не могут быть виртуальными.

Ключевое слово override
Чтобы явным образом указать, что мы хотим переопредлить функцию, а не скрыть ее, в производном классе после списка параметров функции указывается слово override
```cpp
#include <iostream>
  
class Person
{
public:
    Person(std::string name): name{name}
    { }
    virtual void print() const  // виртуальная функция
    {
        std::cout << "Name: " << name << std::endl;
    }
private:
    std::string name;
};
class Employee: public Person
{
public:
    Employee(std::string name, std::string company): Person{name}, company{company}
    { }
    void print() const override // явным образом указываем, что функция переопределена
    {
        Person::print();
        std::cout << "Works in " << company << std::endl;
    }
private:
    std::string company;
};
  
int main()
{
    Person tom {"Tom"};
    Person* person {&tom};
    person->print();     // Name: Tom
    Employee bob {"Bob", "Microsoft"};
    person = &bob;
    person->print();     // Name: Bob
                        // Works in Microsoft
}
```
То есть здесь выражение
```cpp
void print() const override
```
указывает, что мы явным образом хотим переопределить функцию print. Однако может возникнуть вопрос: в предыдущем примере мы не указывали override для вирутальной функции, но переопределение все равно работало, зачем же тогда нужен override? Дело в том, что override явным образом указывает компилятору, что это переопределяемая функция. И если она не соответствует виртуальной функции в базовом классе по списку параметров, возвращаемому типу, константности, или в базовом классе вообще нет функции с таким именем, то компилятор при компиляции сгенерирует ошибку. И по ошибке мы увидим, что с нашей переопределенной функцией что-то не так. Если же override не указать, то компилятор будет считать, что речь идет о скрытии функции, и никаких ошибок не будет генерировать, компиляция пройдет успешно. Поэтмоу при переопределении виртуальной функции в производном классе лучше указывать слово override

При этом стоит отметить, что виртуальную функцию можно переопределить по всей иерархии наследования в том числе не в прямых производных классах.

Принцип выполнения виртуальных функций
Стоит отметить, что виртальные функции имеют свою цены - объекты классов с виртуальными функциями требуют немного больше памяти и немного больше времени для выполнения. Поскольку при создании объекта полиморфного класса (который имеет виртуальные функции) в объекте создается специальный указатель. Этот указатель используется для вызова любой виртуальной функции в объекте. Специальный указатель указывает на таблицу указателей функций, которая создается для класса. Эта таблица, называемая виртуальной таблицей или vtable, содержит по одной записи для каждой виртуальной функции в классе.

Когда функция вызывается через указатель на объект базового класса, происходит следующая последовательность событий
- Указатель на vtable в объекте используется для поиска адреса vtable для класса.
- Затем в таблице идет поиск указателя на вызываемую виртуальную функцию.
- Через найденный указатель функции в vtable вызывается сама функция. В итоге вызов виртуальной функции происходит немного медленнее, чем прямой вызов невиртуальной функции, поэтому каждое объявление и вызов виртуальной функции несет некоторые накладные расходы.





