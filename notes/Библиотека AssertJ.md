---
tags:
  - "#Java/Тестирование"
---
**_AssertJ_** - это библиотека для тестирования кода на языке Java. Она предоставляет богатый и выразительный набор методов для написания утверждений (assertions), что делает код тестов более читаемым и понятным. AssertJ позволяет легко проверять ожидаемое поведение кода и утверждать, что определенные условия выполняются или не выполняются.

Ниже приведены основные характеристики и особенности библиотеки AssertJ:

1.  **Читаемость кода:** AssertJ использует чейнинг (chaining) и методы, именованные на естественном языке, что делает тесты более читаемыми. Это позволяет легко понимать, что именно проверяется в каждом утверждении.

2.  **Богатые утверждения:** AssertJ предоставляет богатый набор методов для проверки различных типов данных, таких как строки, коллекции, массивы, числа и т.д. Эти методы покрывают большую часть типовых проверок, которые могут потребоваться при написании тестов.

3.  **Поддержка пользовательских объектов:** Вы можете легко создавать пользовательские утверждения для собственных классов и объектов. Это делает AssertJ очень гибкой для работы с вашим собственным кодом.

4.  **Сообщения об ошибках:** Если утверждение не выполнилось, AssertJ предоставляет информативное сообщение об ошибке, которое помогает быстро понять, что пошло не так.

5.  **Интеграция с фреймворками тестирования:** AssertJ может быть интегрирована с популярными фреймворками тестирования, такими как JUnit и TestNG.

6.  **Поддержка лямбда-выражений:** С AssertJ можно использовать лямбда-выражения для более сложных проверок и пользовательских утверждений.

7.  **Поддержка Java 8 и выше:** AssertJ полностью совместима с версиями Java, начиная с Java 8.

Написание тестов с использованием библиотеки AssertJ отличается от использования ключевого слова assert и базируется на цепочке методов. Такой подход называется **_fluent API_**.

```java
@Test
**void** **shouldSatisfyContract**() {
    assertThat(check)
        .hasType(Index.class)
        .hasDiagnostic(Diagnostic.INVALID_INDEXES)
        .hasHost(PgHostImpl.ofPrimary());
}
```


## Матчеры

Существует несколько популярных способов описывать утверждения. Кроме вызова обычных методов, популярностью пользуются "матчеры", которые внешне выглядят как мини-язык для описания проверок. 

Матчеры стали популярны в тестовых фреймворках после появления подхода [BDD](https://ru.wikipedia.org/wiki/BDD_(%D0%BF%D1%80%D0%BE%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5)) (Behavior Driven Development, разработка через поведение). Технически, такой подход стремится сделать тесты похожими на словесное описание выполняемой задачи. Это даёт возможность использовать их как документацию людям, которые не умеют программировать (В идеале, на практике всё сложнее). Матчеры заменили собой обычные утверждения на функциях во многих языках:

```java
int[] a = {1, 2, 3};
int[] b = {1, 2, 3};

// Проверка равенства по ссылке
// assert a == b;
assertThat(a).isSameAs(b); // false

// Проверка равенства по значению
// assert Arrays.equals(a, b);
assertThat(a).isEqualTo(b); // true
```

Любой матчер в AssertJ начинается с метода `assertThat(data)`, в которую передаются данные на проверку. Затем `assertThat` возвращает специальный объект, у которого уже можно вызывать различные матчеры для проверки. В AssertJ десятки матчеров для самых разнообразных ситуаций. Такое количество объясняется желанием выдавать максимально точный отчёт о том что произошло.

Предположим, что метод возвращает массив и мы хотим проверить его размер. Для этого можно воспользоваться матчером `isEqualTo`:

```java
int[] data = {1, 2, 3};

// take берет первые n элементов
// assert.equal(take(data, 2).length, 2)
assertThat(ArrayUtils.take(data, 2).length).isEqualTo(3);
```

Этот матчер прекрасно справится с задачей. Но в случае ошибки его вывод не слишком информативен:

```java
Expecting:
 <2>
to be equal to:
 <3>
but was not.
```

Поэтому лучше взять специализированный матчер для проверки размера массива:

```java
assertThat(take(data, 2)).hasSize(3);
```

Тогда вывод расскажет гораздо больше:

```java
Expected size:<3> but was:<2> in:
<[1, 2]>
```

Благодаря тому, что в `assertThat()` передаётся сам массив, а не его длина, у AssertJ появляется возможность выводить содержимое массива в случае ошибки. Это, опять же, упрощает отладку.

Ниже пример некоторых популярных матчеров, полезных в ежедневном тестировании:

```java
char[] data = {'a', 'b', 'c'};
// проверка, что размеры объектов совпадают
assertThat(data).hasSameSizeAs(new int[] {1, 2, 3});
// проверка, что не null
assertThat(data).isNotNull();
// проверка, что выражение равно true
assertThat(true).isTrue();
// проверка, что объект находится в перечне
assertThat(1).isIn(1, 2, 3, 4);
// проверка, что число находится в отрезке [a, b]
assertThat(9).isBetween(9, 11);
// проверка, что число находится в интервале (a, b)
assertThat(10).isStrictlyBetween(9, 11);
```

Кроме того, практически все матчеры обладают как "позитивной" так и "негативной" версией:

```java
assertThat(data).isNull();
assertThat(data).isNotNull();

assertThat(true).isTrue();
assertThat(false).isFalse();

assertThat(1).isIn(1, 2, 3, 4);
assertThat(1).isNotIn(2, 3, 4, 5);
```

Отдельно стоит выделить матчеры, которые работают с экземплярами классов. Для Java, как для ООП-языка, это особенно важно. В AssertJ есть матчеры, которые могут "обходить" переданные экземпляры классов и сравнивать их по отдельным полям.

```java
// сравнение по ссылке
assertThat(obj1).isEqualTo(obj1);
// Каждое поле одного объекта сравнивается с соответствующим полем другого.
assertThat(obj1).isEqualToComparingFieldByField(obj2);

// далее рассмотрим только названия методов
// сравниваем, игнорируя поля, равные null
.isEqualToIgnoringNullFields()
// сравниваем только по указанному перечню полей
.isEqualToComparingOnlyGivenFields()
// все поля объекта равны null
.hasAllNullFieldsOrProperties()
```

Весь перечень подобных матчеров и примеров их использования можно найти в официальной документации [AbstractObjectAssert.](https://joel-costigliola.github.io/assertj/core-8/api/org/assertj/core/api/AbstractObjectAssert.html)

Библиотека _AssertJ_ позволяет собирать несколько тестов в один вызов:

```java
public static void testMethod() {
    var a = "Hello, world!";
    assertThat(a)                // утверждаем, что:
            .startsWith("Hello") // строка начинается с подстроки "Hello"
            .contains("llo, ")   // содержит строку "llo, "
            .endsWith("!");      // заканчивается строкой "!"
    }
```
### Методы AssertJ

`assertThat(actual).isEqualTo(expected)` Проверяет, что actual равно expected. Подходит для сравнения примитивных типов данных и объектов.

`assertThat(actual).isNotEqualTo(expected)` Проверяет, что actual не равно expected.

`assertThat(actual).isNotNull()` Проверяет, что actual не является null.

`assertThat(actual).isNull()` Проверяет, что actual является null.

`assertThat(actual).isInstanceOf(expectedClass)` Проверяет, что actual является экземпляром класса expectedClass.

`assertThat(actual).isNotInstanceOf(unexpectedClass)` Проверяет, что actual не является экземпляром класса unexpectedClass.

`assertThat(actual).isTrue()` Проверяет, что actual равно true.

`assertThat(actual).isFalse()` Проверяет, что actual равно false.

`assertThat(actual).isGreaterThan(expected)` Проверяет, что actual больше чем expected.

`assertThat(actual).isLessThan(expected)` Проверяет, что actual меньше чем expected.

`assertThat(actual).isGreaterThanOrEqualTo(expected)` Проверяет, что actual больше или равно expected.

`assertThat(actual).isLessThanOrEqualTo(expected)` Проверяет, что actual меньше или равно expected.

`assertThat(actual).isBetween(start, end)` Проверяет, что actual находится между start и end.

`assertThat(actual).isIn(array)` Проверяет, что actual содержится в массиве array.

`assertThat(actual).isNotIn(array)` Проверяет, что actual не содержится в массиве array.

`assertThat(actual).hasSize(expectedSize)` Проверяет, что размер коллекции actual равен expectedSize.

`assertThat(actual).contains(expectedElement)` Проверяет, что коллекция actual содержит элемент expectedElement.

`assertThat(actual).doesNotContain(unexpectedElement)` Проверяет, что коллекция actual не содержит элемент unexpectedElement.

`assertThat(actual).startsWith(expectedPrefix)` Проверяет, что строка actual начинается с подстроки expectedPrefix.

`assertThat(actual).endsWith(expectedSuffix)` Проверяет, что строка actual заканчивается подстрокой expectedSuffix.

Тут перечислены методы библиотеки AssertJ с их описаниями:

**`as(Description description)`**:
    - Устанавливает описание для утверждения, которое будет вызвано далее.
    
**`as(String description, Object... args)`**:
    - Устанавливает описание для утверждения, которое будет вызвано далее.
    
**`extracting(Function<? super ACTUAL, ? extends Object> extractor)`**:
    - Использует заданную функцию для извлечения значения из объекта для тестирования, извлеченное значение становится новым объектом для тестирования.
    
**`extracting(Function<? super ACTUAL, Object>... extractors)`**:
    - Использует заданные функции для извлечения значений из объекта для тестирования в список, этот новый список становится объектом для тестирования.
    
**`extracting(String... propertiesOrFields)`**:
    - Извлекает значения указанных полей/свойств из объекта для тестирования в список, этот новый список становится объектом для тестирования.
    
**`getComparatorsByType()`**:
    - Возвращает компараторы по типу.
    
**`hasAllNullFieldsOrProperties()`**:
    - Проверяет, что у объекта все поля или свойства являются null.

**`hasAllNullFieldsOrPropertiesExcept(String... propertiesOrFieldsToIgnore)`**:
    - Проверяет, что у объекта все поля или свойства являются null, за исключением указанных.
    
**`hasFieldOrProperty(String name)`**:
    - Проверяет, что у объекта есть указанное поле или свойство.
    
**`hasFieldOrPropertyWithValue(String name, Object value)`**:
    - Проверяет, что у объекта есть указанное поле или свойство с заданным значением.
    
**`hasNoNullFieldsOrProperties()`**:
    - Проверяет, что у объекта нет null полей или свойств.
    
**`hasNoNullFieldsOrPropertiesExcept(String... propertiesOrFieldsToIgnore)`**
    - Проверяет, что у объекта нет null полей или свойств, за исключением указанных.
    
**`isEqualToComparingFieldByField(Object other)`**:
    - Проверяет, что объект равен данному объекту на основе сравнения полями.
    
**`isEqualToComparingFieldByFieldRecursively(Object other)`**:
    - Проверяет, что объект равен данному объекту на основе рекурсивного сравнения полей.
    
**`isEqualToComparingOnlyGivenFields(Object other, String... propertiesOrFieldsUsedInComparison)`**:
    - Проверяет, что объект равен данному объекту, используя сравнение только указанных полей.
    
 **`isEqualToIgnoringGivenFields(Object other, String... propertiesOrFieldsToIgnore)`**:
    - Проверяет, что объект равен данному объекту, игнорируя указанные поля.
    
 **`isEqualToIgnoringNullFields(Object other)`**:
    
    - Проверяет, что объект равен данному объекту, игнорируя null поля.
    
 **`newObjectAssert(Object objectUnderTest)`**:
    - Создает новое утверждение для заданного объекта.
    
**`returns(T expected, Function<ACTUAL, T> from)`**:
    - Проверяет, что объект возвращает ожидаемое значение из указанной функции.
    
 **`usingComparatorForFields(Comparator<T> comparator, String... propertiesOrFields)`**:
    - Устанавливает компаратор для сравнения полей с указанными именами.
    
 **`usingComparatorForType(Comparator<? super T> comparator, Class<T> type)`**:
    - Устанавливает компаратор для сравнения полей с указанным типом.
    
 **`usingRecursiveComparison()`**:
    - Включает стратегию сравнения полей по рекурсии при вызове цепочечного утверждения isEqualTo.
    
 **`usingRecursiveComparison(RecursiveComparisonConfiguration recursiveComparisonConfiguration)`**:
    - То же самое, что и usingRecursiveComparison(), но позволяет указать собственную конфигурацию рекурсивного сравнения.