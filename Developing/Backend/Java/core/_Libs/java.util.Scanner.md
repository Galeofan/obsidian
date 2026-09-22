```java
package ru.job4j.io;

import java.util.Scanner;

public class Greeting {
    public static void main(String[] args) {
        Scanner input = new Scanner(System.in);
        System.out.print("Добро пожаловать. Как Вас зовут? ");
        String name = input.nextLine();
        System.out.println(name + ", рад Вас видеть!");
    }
}
```

<font color="#31859b">nextLine()</font> - считывает все символы строки после нажатия на клавишу Enter

Постоянный опрос пользователя через while:
```java
package ru.job4j.io;

import java.util.Scanner;

public class Dendy {
    public static void main(String[] args) {
        Scanner input = new Scanner(System.in);
        boolean run = true;
        while (run) {
            System.out.println("Игры.");
            System.out.println("1. Танчики");
            System.out.println("2. Супер Марио");
            System.out.println("3. Выйти");
            System.out.print("Введите пункт меню, чтобы начать игру: ");
            int select = Integer.parseInt(input.nextLine());
            if (select == 3) {
                System.out.println("Игра завершена.");
                run = false;
            } else if (select == 1) {
                System.out.println("Танчики загружаются ... ");
            } else if (select == 2) {
                System.out.println("Супер Марио загружается ... ");
            } else {
                System.out.println("Такой игры нет.");
            }
            System.out.println();
        }
    }
}
```

В строке ниже производится преобразование введенной строки к целому числу
```java
int select = Integer.parseInt(input.nextLine());
```
Важно! В данной программе вместо метода parseInt можно сразу читать значение с помощью метода input.nextInt(), но только при условии, что пользователем введено только одно значение (одну цифру). А так как гарантировать это мы не можем, используется метод parseInt(input.nextLine()), который считывает введённое пользователем значение в виде строки и превращает его в число. Например, если пользователь введёт "1 2 3" при использовании нами nextInt(), то в цикле while этим методом считаются подряд 3 значения, и будут выполнены команды по всем трём пунктам меню. Такое поведение программы недопустимо. А если пользователь сделает ввод "1 2 3" в случае использования нами метода parseInt(input.nextLine(), то программа выбросит исключение, которое можно обработать путём выдачи пользователю сообщения об ошибке ввода.