---
tags: Java/ОсновыЯзыка
--- 
# If / else

Условие if / else
```java
int num1 = 6;  
int num2 = 4;  
if(num1>num2){  
    System.out.println("Первое число больше второго");  
}  
else{  
    System.out.println("Первое число меньше второго");  
}
```

Условие If / else if / else
``` java
int num1 = 6;  
int num2 = 8;  
if(num1>num2){  
    System.out.println("Первое число больше второго");  
}  
else if(num1<num2){  
    System.out.println("Первое число меньше второго");  
}  
else{  
    System.out.println("Числа равны");  
}
```
Можно написать неограниченное кол-во else if операторов.

# Switch

```java
public class Main {  
    public static void main(String[] args) {  
        int num = 8;  
        switch(num){  
  
            case 1:  
                System.out.println("число равно 1");  
                break;  
            case 8:  
                System.out.println("число равно 8");  
                num++;  
                break;  
            case 9:  
                System.out.println("число равно 9");  
                break;  
            default:  
                System.out.println("число не равно 1, 8, 9");  
        }  
    }  
}
```
`case n:` Где n - целое число 

Важной особенностью работы switch является то, что программа просто перепрыгивает на нужную строку (на нужный блок кода) и затем выполняет все блоки кода до конца switch. Не только тот блок кода, который соответствует значению внутри switch, а все блоки кода до конца switch.

Если вы хотите, чтобы в каждом случае выполнялся только один блок кода, на который перепрыгнуло выполнение программы, вам после этого блока кода нужно написать оператор break;

У последнего варианта в switch можно break не писать, все равно блок кода там последний.

Если подходящий вариант не найдется, работа оператора switch завершится, и программа продолжит выполнять тот код, который написан после фигурных скобок оператора switch.

Однако в switch есть возможность сделать поведение, аналогичное ветке else из if-else. Для этого нужно использовать ключевое слово default.

Если ни один case вариант внутри switch не совпал со значением выражения и в switch объявлен default, будет выполнен его код.

```java
int temperature = 40; // Нужна скорая
switch(temperature) {
   case 36:
      System.out.println("Низкая");
      break;

   case 37:
      System.out.println("Нормальная");
      break;

   case 38:
      System.out.println("Высокая");
      break;

   default:
      System.out.println("Нужна скорая");
}
```

Несколько вариантов case-блоков, использующих один код, можно группировать.

```java
public class Practicum {
    public static void main(String[] args) {
        int month = 5;
        switch (month) {
            case 1: // январь
            case 3: // март
            case 5: // май
            case 7: // июль
            case 8: // август
            case 10: // октябрь
            case 12: // декабрь
                System.out.println("В этом месяце 31 день.");
                break;

            case 2: // февраль
                System.out.println("В этом месяце 28 или 29 дней.");
                break;

            case 4: // апрель
            case 6: // июнь
            case 9: // сентябрь
            case 11: // ноябрь
                System.out.println("В этом месяце 30 дней.");
                break;

            default:
                System.out.println("Месяца с таким номером не существует.");
        }
    }
}
```


==Иногда по break не выходит из программы, тогда лучше использовать System.exit(0).

>[!note]
>Метод System.exit() используется в языке Java для завершения программы. Этот метод на вход принимает значение типа int. Обычно это 0, что говорит о том, что программа завершается без ошибок. Любое другое значение говорит о том, что программа завершилась с ошибкой. 

Не все типы можно использовать в качестве значений для case внутри оператора switch. Сейчас там можно использовать литералы таких типов:

- целые типы: byte, short, int
- тип char
- тип String
- значения любого enum-типа

Никаких других типов писать в качестве значений внутри case нельзя.

>**Примечание.** Если вы используете enum внутри switch, можно не писать имя класса перед каждым значением в case. Достаточно просто написать значение.


## Switch-выражения из Java 14

Чтобы получить значение из switch-выражения, раньше приходилось создавать отдельную переменную и постоянно использовать break;. Вот как это выглядело:

```java
String season;
switch (month) {
    case JANUARY:
    case FEBRUARY:
        season = "winter";
        break;
    case MARCH:
    case APRIL:
    case MAY:
        season = "spring";
        break;
    case JUNE:
    case JULY:
    case AUGUST:
        season = "summer";
        break;
    case SEPTEMBER:
    case OCTOBER:
    case NOVEMBER:
        season = "autumn";
        break;
    case DECEMBER:
        season = "winter";
        break;
    default:
        throw new IllegalArgumentException();
    	}  
```

В Java 14 появился новый формат записи, который помогает получать результат выбора и записывать выражение компактнее. Если перечислены все возможные варианты, ветка default теперь не нужна:

``` java
String season = switch (month) {
    case JANUARY, FEBRUARY -> "winter"; //несколько вариантов
    case MARCH, APRIL, MAY -> "spring";
    case JUNE, JULY, AUGUST -> "summer";
    case SEPTEMBER, OCTOBER, NOVEMBER -> "autumn";
    case DECEMBER -> "winter"; //oдин вариант
}
```

Но веткой default можно задать сообщение об ошибке:

``` java
String season = switch (month) {
	case "JAN", "FEB" -> "winter";
	default -> throw new IllegalArgumentException("no such case:" + month);
}
```

Если в значение (case) нужно записать выражение, его заключают в фигурные скобки {} и для возврата значения используют ключевое слово yield:

``` java
String season = switch (month) {
    case JANUARY, FEBRUARY -> "winter";
    case MARCH, APRIL, MAY -> "spring";
    case JUNE, JULY, AUGUST -> "summer";
    case SEPTEMBER, OCTOBER, NOVEMBER -> {
        System.out.println("winter is coming!");
        yield "autumn";
    }
    case DECEMBER -> "winter";
}
```

Switch-выражениям не обязательно возвращать определённое значение:

``` java
switch (order) {
	case NATURAL -> Arrays.sort(strings, Comparator.naturalOrder());
	case REVERSE -> Arrays.sort(strings, Comparator.reverseOrder());
} 
```

Подробнее о switch-выражениях пишут на сайте OpenJDK — [JEP 361: Switch Expressions](https://openjdk.java.net/jeps/361)

## Java 17
💡 В Java 17 появился Switch expressions, с лямбда-синтаксисом:

```java
int temperature = 40; { // Нужна скорая    System._out_.println( switch( temperature ) 
        case 35, 36 -> "Низкая";  
        case 37 -> "Нормальная";  
        case 38, 39 -> "Высокая";  
        default -> "Нужна скорая";    
        } );
```

Можно использовать простые типы **byte, short, char, int**, String? 

