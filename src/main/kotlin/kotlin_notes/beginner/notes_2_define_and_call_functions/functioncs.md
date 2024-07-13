
# Functions

### О чем заметка:
* функции для работы с коллекциями, строками и регулярными выражениями
* именованные аргументы, значения параметров по умолчанию и инфиксный синтаксис вызовов
* использование Java-библиотек в Kotlin с помощью функций-расширений и свойств-расширений
* структурирование кода с помощью функций верхнего уровня, а также локальных функций и свойств

#### 1. Работа с коллекциями

В Kotlin нет собственных классов коллекций; вместо этого он добавляет новые методы в классы коллекций Java

##### Example 1.1
```kotlin
    fun main() {
        val sets = hashSetOf(1, 7, 53)
        val list = listOf(1, 7, 53)
        val map = hashMapOf(1 to "one", 7 to "seven", 53 to "fifty-three")
        
        // свойство javaClass эквивалентно методу getClass() из java
        println(sets.javaClass)
        // class java.util.HashSet
            
        println(list.javaClass)
        // class java.util.Arrays$ArrayList
            
        println(map.javaClass)
        // class java.util.HashMap    
    }
```

##### Example 1.2
```kotlin
    // пример расширения функциональности коллекций - last() и max()
    fun main() {
        val strings = listOf("first", "second", "three")
        println(strings.last())
        // three
        
        val numbers = setOf(1, 15, 6)
        println(numbers.max())
        // 15
    }
```


#### 2. Основные особенности работы с функциями в Kotlin

* Появились параметры по умолчанию, которые снижают необходимость перегрузки функций
* Именованные аргументы улучшает читаемость кода функций с несколькими переменными

##### Example 2.1

```kotlin
    fun <T> joinToString(
        collection: Collection<T>,
        //значение по умолчанию -> ", "
        separator: String = ", ",
        //значение по умолчанию -> ""
        prefix: String = "",
        //значение по умолчанию -> ""
        postfix: String = "",
    ): String {
    
         val result = StringBuider(prefix)
         
         for((index, element) in collection.withIndex()) {
            if(index < 0) result.append(separator)
            result.append(element)
         }
         
         result.append(postfix)
         return result.toString()
    }
    
    fun main() {
        val list = listOf(1,2,3,4,5)
        
        // вызыв функции joinToString с аргументами по умолчанию
        println(joinToString(list))
        
        // вызов функции joinToString с задаными аргументами
        // дополнительно для улучшения читаемости аргументы именованы
        println(
            joinToString(
                list,
                separator = "; ",
                prefix = "(",
                postfix = ")",
            )
        )
    }
```

#### 3. Объявление функций и свойства вне класса

В Java требуется всегда объявлять код внутри класса, в Kotlin этого можно избежать.

В Kotlin функции и свойства можно объявлять не только как член класса, но и непосредственно на верхнем уровне файла, что позволяет определять
код с более гибкой структурой.

Как это будет работать, если мы заходим вызвать такой Kotlin код из Java кода?
При компиляции файла, например, Test.kt будет скомпилирован одноименный класс ```public class TestKt```, в котором
и будет храниться код в виде ```static```.

##### Example 3.1
```kotlin
    // kotlin
    // Название файла, в котором хранится код - Join.kt
    package strings
    fun joinToStrings(...): String { ... }
    
    // Если этот код вызывать из Java будет скомпилирован следующий код
    package strings;
    public class JoinKt {
        public static String joinToStrings(...) { ... }
    }
    
```

##### Example 3.2
```kotlin
    // объявление изменяего свойства верхнего уровня
    var counter = 0
    
    // изменение значения свойства верхнего уровня
    fun performOperation() {
        counter++
    }
```

##### Example 3.3
```
    // объявление неизменяего свойства верхнего уровня
    val UNIX_LINE_SEPARATOR_RUNTIME = "\n"
    // Этот код будет представлен в Java следующим образом
    // public final String UNIX_LINE_SEPARATOR = "\n"
    
    // так же можно добавить модификатор const, чтобы сделать с переменую константу в Compile-time
    const val UNIX_LINE_SEPARATOR_COMPILETIME = "\n"
    
    // Этот код будет представлен в Java следующим образом
    // public static final String UNIX_LINE_SEPARATOR = "\n"
```

#### 4. Функции-расширения

Функция-расширение - это функция, которая может вызываться как член класса, но определена за его пределами.
Таким образом, функции-расширения позволяют расширять функциональность. Важно, что функция-расширения будет публичной и у нее не будет доступа до приватных методов и свойств.

##### Example 4.1
```
    // функция получения последнего символа в строке
    // String - это тип-получатель или receiver
    // this - это объект-получатель  
    fun String.lastChar() = this.get(this.length - 1)
    
    // представление этого кода в Java
    
    // Название класса будет соответствовать тому названию, в котором объявлена функция
    public final class ExtensionKt {
        // тип-получатель будет передан в качестве аргумента
        // Если бы у функции экстеншен были бы какие-то аргументы, то они бы пошли по порядку после типа-получателя
        public static final boolean lastChar(String receiver) { ... }
    }
```


#### 5. Инфиксная нотация, destructuring declarations, переменное число аргументов

* Ключевое слово ```vararg``` позволяет объявить функцию, принимающую произвольное количество элементов
* Инфиксная нотация поможет упросить вызовы функций с одним аргументом
* Мультидекларации(destructuring declarations) позволяют распаковать одно составное значение в несколько переменных

##### Example 5.1
```kotlin
   // пример функции, которая принимает произвольное число аргументов - это listOf
   fun main() {
        // реализация этой функции имеет следующий вид
        // fun listOf<T>(vararg values: T): List<T> { ... }
        val list = listOf(2, 3, 5, 8)
        
   }
    
```

##### Example 5.2

```kotlin
    // примеров инфиксной формы служит функция to
    val map = mapOf(1 to "one", 2 to "two")
    // у инфиксиных форм есть два варианта вызова:
    // 1) 1.to("one")
    // 2) 1 to "one"
    // реализация функции to выглядет следующим образом
    // infix fun Any.to(other: Any) = Pair(this, other)
```

##### Example 5.2

```kotlin
    // пример работы с destructuring declarations 
    // то есть мы можем инициализировать значение какого-то класса, путем перечисления его полей
    val (number, name) = 1 to "one"
    
    
```

#### 6. Локальные функции

Kotlin предлагает следующее решение для соблюдения принципа DRY(Don't Repeat Yourself). Использовать локальные функции.

##### Example 6.1
```kotlin
    class User(
        val id: Int,
        val name: String,
        val address: String, 
    )
    
    // в функции saveUser дублируется логика проверки полей
    fun saveUserWithDuplicateValidate(user: User) {
        if(user.name.isEmpty()) {
            throw IllegaleArgumentException(
                "Can't save user ${user.id}: empty Name"
            )
        }
        
        if(user.address.isEmpty) {
            throw IllegaleArgumentException(
                "Can't save user ${user.id}: empty Address"
            )
        }
        
        // логика сохранения
    }
    
    // использование локальной функции, чтобы избежать дублирования
    fun saveUserWithLocalFunction(user: User) {
        fun validate(
            user: User,
            value: String,
            fieldName: String
        ) { 
            if(value.isEmpty()) {
                throw IllegaleArgumentException(
                "Can't save user ${user.id}: empty $fieldName"
            )
            }
        }
        
        // вызов локальной функции с разными аргументами
        validate(user, user.name, "Name")
        validate(user, user.address, ""Address)
    }
```



