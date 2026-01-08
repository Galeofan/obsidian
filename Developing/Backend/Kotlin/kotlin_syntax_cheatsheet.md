# 🧠 Kotlin Syntax Cheatsheet

## 🔹 Основы
```kotlin
// Однострочный комментарий
/* Многострочный комментарий */

val x = 10       // неизменяемая переменная
var y = 20       // изменяемая переменная

println("Hello, Kotlin!")
```

## 🔹 Типы данных
```kotlin
val a: Int = 10
val b: Double = 3.14
val c: Boolean = true
val d: String = "text"
val e: Char = 'A'
val f: List<Int> = listOf(1, 2, 3)
val g: MutableList<String> = mutableListOf("A", "B")
```

## 🔹 Условные конструкции
```kotlin
val result = if (a > b) "A" else "B"

when (x) {
    1 -> println("One")
    in 2..5 -> println("From 2 to 5")
    else -> println("Other")
}
```

## 🔹 Циклы
```kotlin
for (i in 1..5) println(i)
for (item in listOf("A", "B")) println(item)

var i = 0
while (i < 5) {
    println(i)
    i++
}
```

## 🔹 Функции
```kotlin
fun sum(a: Int, b: Int): Int = a + b

fun greet(name: String = "User") {
    println("Hello, $name")
}

fun multiply(a: Int, b: Int): Int {
    return a * b
}
```

## 🔹 Классы и объекты
```kotlin
class Person(val name: String, var age: Int) {
    fun speak() = println("My name is $name")
}

val p = Person("Andrey", 25)
p.speak()
```

## 🔹 Наследование и интерфейсы
```kotlin
open class Animal {
    open fun sound() = println("Animal sound")
}

class Dog : Animal() {
    override fun sound() = println("Bark")
}

interface Drivable {
    fun drive()
}

class Car : Drivable {
    override fun drive() = println("Driving...")
}
```

## 🔹 Data-классы и Enum
```kotlin
data class User(val id: Int, val name: String)

val user = User(1, "Andrey")
println(user.copy(name = "Alex"))

enum class Direction { NORTH, SOUTH, EAST, WEST }
```

## 🔹 Null safety
```kotlin
val s: String? = null
println(s?.length)       // безопасный вызов - если null то будет null без выброса исключения
println(s ?: "empty")     // оператор Элвиса

s!!.length  // бросит NullPointerException, если s == null
```

## 🔹 Лямбды и коллекции
```kotlin
val numbers = listOf(1, 2, 3, 4, 5)
val doubled = numbers.map { it * 2 }
val filtered = numbers.filter { it % 2 == 0 }

numbers.forEach { println(it) }
```

## 🔹 Extension-функции
```kotlin
fun String.capitalizeWords(): String =
    split(" ").joinToString(" ") { it.replaceFirstChar { c -> c.uppercase() } }

println("hello kotlin".capitalizeWords())
```

## 🔹 Объекты и companion
```kotlin
object Logger {
    fun log(msg: String) = println(msg)
}

class MathUtils {
    companion object {
        fun add(a: Int, b: Int) = a + b
    }
}
```

## 🔹 Sealed классы и when
```kotlin
sealed class Result
class Success(val data: String) : Result()
class Error(val msg: String) : Result()

fun handle(result: Result) = when (result) {
    is Success -> println(result.data)
    is Error -> println(result.msg)
}
```

## 🔹 Coroutines (основы)
```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    launch {
        delay(1000L)
        println("World!")
    }
    println("Hello,")
}
```

## 🔹 Destructuring
```kotlin
val (id, name) = User(1, "Andrey")
println("id=$id, name=$name")
```

---

**Полезно помнить:**
- `val` — неизменяемая ссылка
- `var` — изменяемая
- Все классы и функции по умолчанию `final`
- Все параметры по умолчанию `val`
- Kotlin компилируется в байткод JVM и полностью совместим с Java

