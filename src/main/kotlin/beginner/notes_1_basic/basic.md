package beginner.notes_1_basic

# Basic Kotlin

### О чем заметка:
* объявление функций, переменных классов, перечислений и свойств;
* управляющие структуры;
* автоматический вывод типов из контекса;
* перехват исключений;

### Основная часть

#### 1. Объявление функций и переменных

Объявление функции начинается с ключевого слова `fun`. Ключевые слова `val` и `var`
служат для объявления неименяемых и изменяемых переменных.


##### Example 1.1
```
    Click button[sda]
    // Пример объявления функции
    fun max(a: Int, b: Int) : Int {
        return if(a > b) a else b
    }
    
    fun main() {
        println(max(10, 100))
    }
```

##### Example 1.2
```
    // Пример объявления переменых val и var
    fun main() {
        val answer = 42
        // Ошибка компиляции
        // answer = 100 
        
        var message = "Hello!"
        message = "Bye!" // Ок
    }
```

#### 2. Работа со строками

Строковые шаблоны помогают избежать лишних операций конкатенации строк. 
Добавьте к переменой префикс ```$``` или заключите выражение в ```${}``` - и соответствующее значение будет подставлено в строку

##### Example 2.1
```
    fun main(args: Array<String>) {
        if(args.size > 0) {
            println("${Hello, ${args[0]}!}")
        }
    }
```

#### 3. Базовая работа с классами

В Kotlin можно очень лаконично описывать классы

##### Example 3.1
```
    // пример класс c конструктором
    class Person(
        val name: String,
        val isMarried: Boolean,
    ) {
        override fun toString(): String {
            return "Person(name='$name', isMarried=$isMarried)"
        }
    }
    
    fun main() {
        val person = Person("Dima", false)
        println(person)
    }
```

#### 4. Оператор ```if``` теперь является выражением и возвращает значение

##### Example 4.1
```
    // перепишем уже знакомый следующим образом
    fun max(a: Int, b: Int) = if(a > b) a else b
```

Выражения и инструкции

В Kotlin оператор ``ìf`` - это выражение, а не инструкция. Разница между выражениями и инстуркциями
 состоит в том, что выражение имеет значения, которое можно использовать в других выражениях, в то время как
инструкции всегда являются элементами верхнего уровня в охватывающем блоке и не имеют собственного значения.
В Java все управляющие структуры - инструкции. В kotlin большинство управляющих структур, кроме циклом (```for; while; do while```),- выражения.
Возможность комбинировать структуры управления с другими выражениями позволяет емко выражать многие распространенные шаблоны

#### 5. Выражение ```when``` - более мощный аналог выражения ```switch``` в java

##### Example 5.1
```
   // Можно использовать оператор when, как аналог оператора switch из Java
   const val STATUS_CODE = "STATUS_CODE"
   fun errorHandler(errorCode: Int) {
       when(errorCode) {
           in 200..299 -> println("$STATUS_CODE = Success")
           in 300..309 -> println("$STATUS_CODE = Redirection")
           in 400..499 -> println("$STATUS_CODE = Client error")
           in 500..599 -> println("$STATUS_CODE = Server error")
           else -> println("STATUS_CODE Undefined = status")
       }
   }
   
   fun main() {
      val range = 0..1000
      
      val randomStatusCode = range.random()
      
      println(errorHandler(randomStatusCode))
      
   }
```

##### Example 5.2
```
   
   // Можно использовать и просто на последовательную проверку типов сверху вниз
   fun main() {
        val a = Random.nextInt(-2, 3)
 
        when {
            a > 0 -> println("+")
            a < 0 -> println("-")
            else -> println("0")
       }
   }
   
   // В обоих случаях ветка else отрабатывает только в случае ни из условии ранее не было выполнено
``` 

#### 6. Type inference

Исчезла необходимость явно указывать тип переменой во время ее объявления, благодаря механизму type inference.

```
    fun main() {
        // компилятор понимает из контекста, что test имеет тип String
        val test = "Hello world!"
        
        // У переменой testHellow явно указан тип String
        val testHellow: String = "Hello world!"
    }
```

#### 7. Циклы ```while```, ```for```, ```do-while``` похожи на свои аналоги из Java, но теперь цикл ```for``` стал чуточку удобнее.

```
    fun main() {
        // цикл переберет все значения от 100 до 1 с шагом 2
        for(i in 100 downTo 1 step 2) {
           println(i)
        }
    
        // распечатает все значения от A до F
        for(char in 'A'..'F') {
           println(char)
        }
    
        пройдем по все значениям из коллекции lists
        val lists = listOf("One", "Two", "Three", "Four", "Five")
        for(element in lists) {
           println(element)
        }
    }
```

#### 8. Range диапозоны 

Лаконичный синтаксис для диапазонов(1..5). Диапозоны и прогрессии позволяют использовать единый синтаксис и набор абстракций для цикла ```for```, а помощью операторов ```in``` и ```!in``` можно проверить принадлежность значения диапозону

```
    fun recognize(c: Char) = when(c) {
        in '0'..'9' -> "It's a digit!"
        in 'a'..'z' -> "It's a letter"
        else -> "Undefined behavior!" 
    }
    
    fun main() {
        val range = '0'..'z'
        println(recognize(range.random()))
    }

```

#### 9. try, catch, finally

Обработка исключений в Kotlin выполняется почти так же, как в Java, лишь с той разницей, что Kotlin не требует перечислять контролируемые исключения, которые может возбуждать функция


```
    // Использование try как в Java
    fun readNumber(reader: BufferReader) : Int? // не требуется явно указываь, какое исключение может возбудить функция {
        try {
            val line = reader.readLine()
            return Integer.parseInt(line)
        } catch(e: NumberFormatException) {
            return nulll
          // блок finally работает также как в java  
        } finally {
            reader.close
        }
    }
    
    // try можно использовать как выражение
    fun readNumber(reader: BufferedReader) {
        val number = try {
            Integer.parseInt(reader.readLine())
        } catch(e: NumberFormatException) {
            return
        }
        println(number)
    }
```