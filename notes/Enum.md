---
tags:
  - Java/ОсновыЯзыка
  - "#Enum"
---
перечисление (англ. enumerated type, «‎перечисляемый тип»). Он нужен для хранения множества значений — но не любого, а ограниченного. После него пишется имя в [[Code style#Имена|UpperCamelCase]], а затем в фигурных скобках перечисляются элементы ограниченного множества — списком, через запятую. Все элементы перечисления принято писать как константы: в верхнем регистре, разделяя слова внутри названий символами подчёркивания. Дело в том, что перечисляемый тип по сути — это и есть список логически связанных констант. Ведь количество сторон света или времён года вряд ли когда-нибудь изменится.

Поэтому иногда значения перечисления так и называют: константы перечисления. Каждая из них — static final и не может быть изменена после создания.

```java
enum ИмяТипа
{
   ЗНАЧЕНИЕ1,
   ЗНАЧЕНИЕ2,
   ЗНАЧЕНИЕ3
}
```

Где ИмяТипа — это имя нового типа (класса), а в скобках через запятую перечислены возможные значения: Значение1, Значение2, Значение3.

```java
public enum DayOfWeek {
    MONDAY,
    TUESDAY,
    WEDNESDAY,
    THURSDAY,
    FRIDAY,
    SATURDAY,
    SUNDAY
}
```

>Если в блоке кода  `enum` рядом с набором констант есть метод, то после последнего элемента перечисления нужно поставить символ `;`.

## Переменная с типом enum

Так же, как создаются переменные с типом String или int, можно создавать переменные с типом объявленного перечисления.

```java
public class Film {
    String title;
    FilmGenre genre;
}

enum FilmGenre {
    FAMILY, // семейный
    COMEDY, // комедия
    DOCUMENTARY, // документальный
    DRAMA, // драма
    HORROR, // фильм ужасов
    FANTASY, // фантастический
    THRILLER // триллер
}
```

Значение переменной genre, как и других переменных c типом `enum`, нужно инициализировать в упрощённом виде — без оператора `new`.

```java
FilmGenre genre = FilmGenre.COMEDY;
```

Это связано с тем, что переменной с типом `enum` можно присвоить только то значение, которое определено в перечислении, а значит, существует в единственном экземпляре на всю программу. В примере с фильмами переменной genre можно присвоить только одно из значений перечисления `FilmGenre`.

Элементы перечисления можно сравнивать друг с другом с помощью оператора `==`. Метод equals() по назначению совпадает с оператором `==`.

Если сравнивать объект с элементом перечисления через метод, то equals всегда должен быть вызван у элемента перечисления, а не у объекта, с которым мы этот элемент пытаемся сравнить. Иначе возникнет ошибка `NullPointerException`.

```java
DayOfWeek.TUESDAY.equals(day)
```

### values()

возвращает массив всех значений типа enum, в том же порядке, в котором они объявлены. Чаще всего этот метод используется в тех частях приложения, где нужно предоставить все возможные значения: в выпадающих списках, перечислениях доступных опций и так далее.

```java
Day[] days = Day.values(); // В переменную days сохраняется массив значений типа Day (7 штук)
for (Day day: days)         // Выводим содержимое массива на экран:

   System.out.println(day); // MONDAY
                            // TUESDAY
                            // WEDNESDAY
                            // THURSDAY
                            // FRIDAY
                            // SATURDAY
                            // SUNDAY

System.out.println(days[2]); // WEDNESDAY
```

### valueOf()

Для обратного преобразования (строки в объект Day). Находит и возвращает заданное значение enum. Если элемент не будет найден, выполнение метода завершится с ошибкой. Такой метод будет полезен, когда одному приложению нужно принять константу перечисления от другого приложения.

```java
Day day = Day.valueOf("MONDAY")
```

Преобразование в число и обратно

### name()

Возвращает имя элемента перечисления.

На первый взгляд может показаться, что методы name() и toString() дают одинаковый результат, но это не так.

Разница вот в чём:

·         метод name() объявлен с модификатором final — его нельзя переопределять, но можно уверенно использовать для получения оригинального имени элемента перечисления;

·         а метод toString() может быть переопределён — с его помощью можно вернуть адаптированное и более понятное для пользователя имя константы.

### ordinal()

возвращает порядковый номер константы. Вызывать его нужно не у класса enum, а у значения enum:

```java
System.out.println(Day.MONDAY.ordinal()); // 0

System.out.println(Day.FRIDAY.ordinal()); // 4

System.out.println(Day.SUNDAY.ordinal()); // 6
```

Преобразование в строку и обратно

Чтобы преобразовать объект типа enum в строку, у него нужно вызвать метод **toString()**.

```java
String str = Day.MONDAY.toString();
```

Для этого нужно вызвать метод ordinal():

```java
int index = Day.MONDAY.ordinal();
```

Для обратного преобразования (числа в объект Day) нужно воспользоваться конструкцией подлиннее:

```java
Day day = Day.values()[2];

Day day = Day.MONDAY; // Понедельник

int index = day.ordinal(); // Получаем индекс понедельника (0)

Day newDay = Day.values()[index+2]; // День недели на 2 дня позже понедельника
```

Важный момент, т.к. значения типа enum представляют собой фиксированный набор констант, их можно сравнивать через `==`. Не может существовать двух одинаковых объектов MONDAY с разными ссылками. Каждый объект-значение типа enum существует только в единственном экземпляре. Поэтому сравнение переменных типа enum через `==` всегда будет работать.

Внутри каждого перечисления можно объявить поля и методы, а также конструкторы (для этого надо передать аргументы в перечисление)

[Java | Перечисления enum](https://metanit.com/java/tutorial/3.8.php)