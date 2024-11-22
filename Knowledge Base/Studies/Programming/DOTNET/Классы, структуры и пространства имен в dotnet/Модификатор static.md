[[Классы, структуры и пространства имен в dotnet]] 

Кроме обычных полей, методов, свойств классы и структуры могут иметь статические поля, методы, свойства. Статические поля, методы, свойства относятся ко всему классу/всей структуре и для обращения к подобным членам необязательно создавать экземпляр класса / структуры.

### Статические поля

Статические поля хранят состояние всего класса / структуры. Статическое поле определяется как и обычное, только перед типом поля указывается ключевое слово `static`. Например, рассмотрим класс Person, который представляет человека:

``` cs 
Person bob = new(68);
bob.СheckAge();     // Уже на пенсии
 
Person tom = new(37);
tom.СheckAge();     // Сколько лет осталось до пенсии: 28
 
// получение статического поля
Console.WriteLine(Person.retirementAge); // 65
// изменение статического поля
Person.retirementAge = 67;
 
class Person
{
    int age;
    public static int retirementAge = 65;
    public Person(int age)
    {
        this.age = age;
    }
    public void СheckAge()
    {
        if (age >= retirementAge)
            Console.WriteLine("Уже на пенсии");
        else
            Console.WriteLine($"Сколько лет осталось до пенсии: {retirementAge - age}");
    }
}
```

На уровне памяти для статических полей будет создаваться участок в памяти, который будет общим для всех объектов класса.

![[Pasted image 20241121211528.png]]


### Статические свойства

Подобным образом мы можем создавать и использовать статические свойства:

``` cs 
Person bob = new(68);
bob.СheckAge();
 
Console.WriteLine(Person.RetirementAge); // 65
 
class Person
{
    int age;
    static int retirementAge = 65;
    public static int RetirementAge
    {
        get { return retirementAge; }
        set { if (value > 1 && value < 100) retirementAge = value; }
    }
    public Person(int age)
    {
        this.age = age;
    }
    public void СheckAge()
    {
        if (age >= retirementAge)
            Console.WriteLine("Уже на пенсии");
        else
            Console.WriteLine($"Сколько лет осталось до пенсии: {retirementAge - age}") ;
    }
}
```

В данном случае доступ к статической переменной retirementAge опосредуется с помощью статического свойства `RetirementAge`.

Таким образом, переменные и свойства, которые хранят состояние, общее для всех объектов класса / структуры, следует определять как статические.

### Статические методы

Статические методы определяют общее для всех объектов поведение, которое не зависит от конкретного объекта. Для обращения к статическим методам также применяется имя класса / структуры:

``` cs 
Person bob = new(68);
Person.CheckRetirementStatus(bob);
 
class Person
{
    public int Age { get; set; }
    static int retirementAge = 65;
    public Person(int age) => Age = age;
    public static void CheckRetirementStatus(Person person)
    {
        if (person.Age >= retirementAge)
            Console.WriteLine("Уже на пенсии");
        else
            Console.WriteLine($"Сколько лет осталось до пенсии: {retirementAge - person.Age}") ;
    }
}
```

В данном случае в классе Person определен статический метод `CheckRetirementStatus()`, который в качестве параметра принимает объект Person и проверяет его пенсионный статус.

### Статический конструктор

Кроме обычных конструкторов у класса также могут быть статические конструкторы. Статические конструкторы имеют следующие отличительные черты:

- Статические конструкторы не должны иметь модификатор доступа и не принимают параметров

- Как и в статических методах, в статических конструкторах нельзя использовать ключевое слово this для ссылки на текущий объект класса и можно обращаться только к статическим членам класса
   
- Статические конструкторы нельзя вызвать в программе вручную. Они выполняются автоматически при самом первом создании объекта данного класса или при первом обращении к его статическим членам (если таковые имеются)

_Статические конструкторы обычно используются для инициализации статических данных, либо же выполняют действия, которые требуется выполнить только один раз_

### Статические классы

Статические классы объявляются с модификатором `static` и могут содержать только статические поля, свойства и методы. Например, определим класс, который выполняет ряд арифметических операций:

``` cs
Console.WriteLine(Operations.Add(5, 4));         // 9
Console.WriteLine(Operations.Subtract(5, 4));    // 1
Console.WriteLine(Operations.Multiply(5, 4));    // 20
 
static class Operations
{
    public static int Add(int x, int y) => x + y;
    public static int Subtract(int x, int y) => x - y;
    public static int Multiply(int x, int y) => x * y;
}
```