### Фреймворк JUnit 5
#### org.junit.jupiter.api.Test
#### org.assertj.core.api.Assertions.*
##### Аннотации
- <font color="#ffff00">@Test</font> - Над каждым методом стоит [аннотация](_Термины.md#Аннотация) <font color="#ffff00">@Test</font>. Аннотация <font color="#ffff00">@Test</font> позволяет запустить каждый [метод](_Термины.md#Метод) по отдельности. В случае с тестированием это удобный механизм.
- <font color="#ffff00">@RepeatedTest(2)</font>  - эта аннотация указывает библиотеке, сколько раз должен быть повторен метод. В данном случае, метод будет повторен 2 раза. Количество повторений указывается в скобках. Аннотация используется с методами.
- <font color="#ffff00">@DisplayName("Full text a scenario of a test")</font> - с помощью этой аннотации можно описать тест предложением.
- <font color="#ffff00">@Disabled</font><font color="#ffff00">("Need to fix impl")</font> - эта аннотация отключает весь тест-класс или тест-метод.
- <font color="#ffff00">@Timeout(3)</font> - эта аннотация принудительно завершает работу теста через указанное количество секунд. Если тест не успевает закончить свою работу, он будет принудительно остановлен и отмечен, как не пройденный.
##### Методы
- <font color="#31859b">assertThat()</font> -  метод который сравнивает два значения: результат вычисления и ожидаемый результат. В качестве аргумента мы указываем выходное значение - output, 
- <font color="#31859b">isEqualTo()</font> - метод в который мы передаем ожидаемое значение - expected, 
- <font color="#31859b">withPrecision()</font> - метод в который передаем точность сравнения - value, которую мы указываем в качестве [аргумента](6.%20Методы.md#Создание%20метода).
- <font color="#31859b">isTrue()</font> - метод проверки булевого выражения на истину
- <font color="#31859b">isFalse()</font> - метод проверки булевого выражения на ложь
- <font color="#31859b">isCloseTo()</font> - метод проверки чисел с плавающей точкой
- <font color="#31859b">containsExactly</font> - метод проверки равенства массивов
- <font color="#31859b">assertNull()</font> - метод проверки объекта на NULL
- <font color="#31859b">assertThrows()</font> - 
```java
void whenConvert140RblThen2Euro() {  
    float input = 140; //Входное значение
    float expected = 2;  //Ожидаемое значение
    float output = Converter.rubleToEuro(input);  //Вызов проверяемого метода
    float value = 0.0001f; //Точность (до десятитысячных)
    assertThat(output).isEqualTo(expected, withPrecision(value));  //Сравнение
}
```

Ещё примеры:
1. Проверка целочисленных значений:
```java
@Test
public void whenEquals() {
    int result = 1;
    int expected = 1;
    assertThat(result).isEqualTo(expected);
}
```

2. Проверка чисел с плавающей точкой:
```java
@Test
public void whenFloatPoint() {
    float result = 1.0000001F;
    float expected = 1F;
    assertThat(result).isCloseTo(expected, offset(0.01F));
}
```

3. Равенство массивов:
```java
@Test
public void whenArray() {
    int[] result = {1, 2, 3};
    int[] expected = {1, 2, 3};
    assertThat(result).containsExactly(expected);
}
```