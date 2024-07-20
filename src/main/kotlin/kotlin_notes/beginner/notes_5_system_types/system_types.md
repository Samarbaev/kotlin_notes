# System types

### О чем заметка:

* типы с поддержкой пустых значений и работа с null
* простые типы и их соответствие типам Java
* Коллекции в Kotlin и их связь с Java

#### 1.Nullable

* Управление поддержкой null в языке kotlin помогает выявить возможные исключения NullPointerException
  на этапе компиляции
* В Kotlin для работы со значением null есть специальные инструменты:
    * ?. - safe call;
    * ?: - "элвис";
    * !! - "оператор бабах";
    * let - функция из библиотеки языка;
* Оператор as? позволяет привести значения к типу и обрабатывать случаи, когда оно имеет несовместимый тип

```kotlin

import java.lang.NullPointerException

// safe call operator - ?.
fun printAllCaps(s: String?) {
    val allCaps: String? = s?.uppercase()
    println(allCaps)
}

// >>> printAllCaps("ABC")
// abc
// >>> printAllCaps(null)
// null
// элвис оператор, если мы хотим вместо null использовать значение по умолчанию
fun foo(s: String?) {
    val t = s ?: ""
}

// оператор "бабах"
fun ignoreNull(s: String?) {
    val sNotNull: String = s!!
    println(sNotNull.length)
}
// >>> println(ignoreNull(null))
// NullPointerException

// оператор безопасного приведения - as?
// если бы мы воспользовали обыным оператором as и у нас не удалось бы привести значение к указанному типу,
// мы бы получили ClassCastException

class Person(val firstName: String, val lastName: String) {
    override fun equals(other: Any?): Boolean {
        // проверить тип и вернет false, если указанный тип недопустим
        val otherPerson = o as? Person ?: return false

        // после безопасного приведения типа переменная otherPerson преобразует к типу Person
        return otherPerson.firstName == firstName &&
                otherPerson.lastName == lastName
    }

    override fun hashCode(): Int =
        firstName.hashCode() * 37 + lastName.hashCode()
}
// >> val p1 = Person("Dmitry", "Jemerov")
// >> val p2 = Person("Dmitry", "Jemerov")
// >> println(p1 == p2)
// true
// >> println(p1.equals(42))
// false

```

#### 2. Допустимость значения null и Java

Выше мы обсудили как работать в Kotlin c null. Как будет происходить обработка null на стыке Java и Kotlin?

1. Java может содержать информацию о nullable-типах и notnull-типах, это достигается с помощью аннотации: @Nullable и
   @NotNull.
   Java(@Nullable + Тype) = Kotlin(Type?)
   Java(@NotNull + Тype) = Kotlin(Type)
2. Также существуют платформенные типы - это типы, для которых Kotlin не может найти информацию о допустимости null.
   В этом случае с ним можно работать как с типом допускающим null или как с типом не допускающим null.
   Java(Type) = Kotlin(Type?) или Kotlin(Type)

``` java

    // дополнительных аннотаций @Nullable и @NotNull - нет
    public class Person {
        private final String name;
        
        public Person(String name) {
          this.name = name;
        }
        
        public String getName() {
          return name;
        }
    }

    // попытаем вызвать java код из kotlin кода
    fun yellAt(person: Person) {
      println(person.name.toUpperCase() + "!!!")
    }
    
    // >> yellAt(Person(null))
    // java.lang.IllegalArgumentException: Parameter specified as non-null...
```

Платформенные типы были введены для того, чтобы Kotlin оставался лаконичными и прагматичным языком.
Можно было бы сделать так, чтобы все типы, поддерживали Nullable, но у этого подхода есть недостаток.
В этом случае было бы много проверок на null для дженерик-типов, например, ArrayList<String> был бы представлен как
ArrayList<String?>?
Тогда бы каждый раз пришлось делать проверку на null

#### 3. Примитивные типы

* Типы пришедшие из Java, интерпретируются в Kotlin как платформенные типы, что позволяет разработчику относиться к ним
  как к типам с поддержкой или без поддержки null
* Типы, представляющие обычные числа(например, Int) выглядят и функционируют как рядовые классы, но обычно компилируются
  в примитивные типы Java
* Kotlin-типы с поддержкой null не могут быть представлены в Java как примитивные типы, поскольку Java значение null
  может храниться только в переменных ссылочных типов.

```kotlin
    data class Person(val name: String, val age: Int? = null) {

    fun isOlderThan(other: Person): Boolean? {
        if (age == null || other.age == null) {
            return null
        }
        return age > other.age
    }
}
// >>> println(Person("Sam", 35).isOlderThan(Person("Amy", 42)))
// false
// >>> println(Person("Sam", 35).isOlderThan(Person("Jane")))
// true
```

#### 4. Тип Any, тип Unit

Тип Any - это супертип всех других типов и аналог типа Object в Java, а тип Unit - аналог void

#### 5. Тип Nothing

Nothing - это подтип всех типов
Тип Nothing используется в качестве типа возвращаемого значения для функций, которые в обычном режиме не завершаются

```kotlin
    
fun error(message: Any): Nothing = throw IllegalStateException(message.toString())

val user: User = request.user ?: error("User not found")
```

#### 6. Коллекции в kotlin

* Класс Array в Kotlin выглядит как обычный обобщенный класс, но компилируется в Java-массив
* Для массивов примитивных типов существуют специальные классы обертки - IntArray, FloatArray и другие
* Коллекции в Kotlin представлены двумя большими классами - изменяемыми и неизменяемыми