
# Classes

### О чем заметка:
* классы и интерфейсы;
* нетривиальные свойства и конструкторы;
* классы данных;
* делегирование;
* ключевое слово ```object```

#### 1. Интерфейсы

Интерфейсы в Kotlin похожи на интерфейсы в Java, но могут включать свойства и реализации функций по умолчанию
(Это доступно в Java только в версии 8)

```kotlin
    interface Clickable {
        
        // обычные объявление метода
        fun click()
        
        // функция с реализацией по умолчанию
        fun showOff() = println("I'm clickable!")
    }
    // При реализации интерфейса Clikable нужно будет переопределить только метод click()
    // можно переопределить и метод showOff, но это необязательно 
    
    // Создадим еще один interface
    interface Focusable {
        
        // обычные объявление метода
        fun click()
        
        // функция с реализацией по умолчанию
        fun showOff() = println("I'm clickable!")
    }
    
    // Если потребуется реализовать 2 интерфейса Clikable и Focusable
    // Никакой из реализаций showOff() не будет использована
    // метод showOff() придется реализовать явно
    
    class Button : Clickable, Focusable {
        ovveride fun click() = println("I was clicked!")
        
        ovveride fun showOff() {
            super<Clickable>.showOff()
            super<Focusable>.showOff()
        }
    }
    
```

#### 2. Все по умолчанию final и public

Многолетняя практика использования Java показала, что, чтобы переопределить функциональности в классе
нужно явно это указать, по этой причине все по умолчанию final в Kotlin

#### 3. Модификатор open и abstract

Модификатор open отменяет работу модификатора final или другими словами, разрешаем наследование

```kotlin
    // открытый класс: другие могут его наследовать
    open class RichButton : Clickable {
        
        // Это закрытая функция: ее невозможно переопределить в подклассе
        fun disable()
        
        // Это открытая функция: ее можно переопределить в подклассе 
        open fun animate() { }
        
        // Переопределение открытой функции также является открытым
        // Если бы хотел закрыть метод click для наследования, то к нему добавился бы модификатор final
        // и наш метод бы выглядет следующим образом  final ovveride fun click() {}
        ovveride fun click() {}
    
    }
    
    // Абстрактный класс: нельзя создать экземпляр этого класса
    abstract class Animated {
        // Это абстракная функция: она не имеет реализации и должна быть переопределена в подклассах
        abstact fun animate()
        
        // Открытая функция в абстрактном классе
        open fun stopAnimated() {...}
        
        // Закрытая функция в абстрактном классе
        fun animatedTwice() {...}
    }
```

#### 4. Модификатор видимости internal

В Kotlin нет модификатора видимости как в Java package private. Пакеты используются только для организации
кода в пространство имен, но не для управления видимостью.

В Kotlin появился новый модификатор видимости ``internal``. Модификатор ``internal`` ограничивает видимость в границе модуля.
Под модулем понимается набор файлов, которые компилируются вместе. Это может быть модуль maven или gradle.

| Модификатор            | Член класса              | Объявление верхнего уровня |
|------------------------|--------------------------|----------------------------|
| `public`(по умолчанию) | доступен повсюду         | доступно повсюду           |
| `internal`             | доступен только в модуле | доступно в модуле          |
| `protected`            | доступен в подклассах    | -                          |
 | `private`              | доступен в классе        | доступно в файле           |


#### 5. Модификатор inner

По умолчанию вложенные классы не становятся внутренними. Чтобы сохранить ссылку на внешний класс, используйте модификатор `inner`
Во вложенных классах, в отличие от внутренних, отсутствует ссылка на внешний класс

```kotlin
    class Outer {
        inner class Inner {
            fun getOtherRef(): Outer = this@Outer
        }
    }
```

#### 6. Sealed classes

Sealed класс определяет строгую иерархию типов. Разница с enum-классом в том, что каждый подтип
sealed класса может иметь более одного экземпляра.

```kotlin
    sealed class UiState {
        object Loading: UiState()
        data class Success(val list: List<String>): UiState()
        object Error: UiState()
    }
```

#### 7. Блок init и конструкторы классов

Блоки инициализации и вторичные конструкторы дают дополнительную гибкость инициализации экземпляров класса

```kotlin
    // использование primary конструктора и блока инициализации
    class User(_nickname: String) {
        val nickname: String
        
        init {
            nickname = _nickname
        }
    }
```

```kotlin
    // использование secondary конструктора
    class MyButton : View {
        // в обоих случаях делегируем выполнение super конструктора(вызывает конструктор родителя)
        constructor(ctx: Context) : super(ctx) {...}
        constructor(ctx: Context, attr: AttributeSet) : super(ctx, attr) {...}
    }   
    
    class MyButton : View {
        // в этом случае делегирует выполнение конструктора this
        constructor(ctx: Context) : this(ctx, MY_STYLE) {...}
        // в этом случае делегирует выполнение конструктора super конструктора
        constructor(ctx: Context, attr: AttributeSet) : super(ctx, attr) {...}
    }
```

#### 8. Идентификатор field, вычисляемые свойства и кастомные геттеры и сеттеры для свойств

Идентификатор `field` используется в методах доступа для ссылки на соответствующее поле, хранящее значение.
В kotlin нет прямого доступа к полям класса, kotlin оперирует свойствами. Но иногда обратиться к полю класса все-таки необходимо

```kotlin
    // напишем кастомный геттер для поля address
    class User(val name: String) {
        var address: String = "unspecified"
         set(value: String) {
          pritnln("log:")
          field = value
         }
    }
    
    // такжк можно присвоить модификатор видимости сеттеру у свойства
    class LengthCounter {
        var counter: Int = 0
        private set
        
        fun addWord(word: String) {
            counter += word.length
        }
    }
```

#### 9. data class

Удобной особенностью `data class` является то, что эти классы автоматически генерируется правильные методы: equals, hashcode, toString, copy.
И дополнительно для реализации поведения destructive declaration методы componentN.

```kotlin
    // для всех свойств объявяленных в первичном конструкторе будут сгенерированы этим методы
    data class User(
        val id: String,
        val name: String,
        val age: Int,
    ) {
       // для свойств объявленых в теле класс - генерация происходить не будет
    }     
```

#### 10. Делегирование

Поддержка делегирования позволяет избавиться от множества делегирующих методов в вашем коде.

```kotlin
    // Напишем свою реализаци CountingSet
    // которая будет подсчитывать количество добавлений элементов в нее
    class CountingSet<T>(
        val innerSet: MutableCollection<T> = HashSet<T>()
    ) : MutableCollection<T> by innerSet {
        
        // innerSet это делегирование реализации MutableCollection 
        // объекту в поле innerSet
        
        var objectsAdded = 0 
     
        // собственная реализация вместо делегирования
        override fun add(element: T): Boolean{
            objectsAdded++
            return  innerSet.add(element)
        }

         // собственная реализация вместо делегирования
        override fun addAll(elements: Collection<T>): Boolean{
            objectsAdded += elements.size
            return innerSet.addAll(elements)
        }
    }

    // при запуске программы мы увидим следующее
    fun main() {
        val cset = CountingSet<Int>()
        cset.addAll(listOf(1,1,2))
        println("${cset.objectsAdded} object were added, ${cset.size} remain")
        // output
        // 3 object were added, 2 remain
    }
```

#### 11. Ключевое слово object

* Объявление объекта - это способ создать singleton в kotlin

```kotlin
    object Payroll {
        val allEmployees = arrayListOf<Person>()
 
        fun calculateSalary() {
            for (person in allEmployees) {
                //
            }
        }
    }
```

* Так же ключевое слово object используется для создания анонимного объекта

```kotlin

import java.awt.event.MouseAdapter
import java.awt.event.MouseEvent

    
    window.addMouseListener(
        object : MouseAdapter() {
            override fun mouseClicked(e: MouseEvent?){
                //
            }     
         
            override fun mouseEntered(e: MouseEvent?){
                //
            }
        }
    )
```

#### 12. Companion object

Companion object используется вместо статических методов и полей в Java

```kotlin
    class Person(val name: String) {
        companion object {
            fun fromJSON(jsonText: String): Person = {
                //
            }
        }
    }
```
