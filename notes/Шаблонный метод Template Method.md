---
tags: Паттерны
---

**Шаблонный метод** — это поведенческий паттерн проектирования, который определяет скелет алгоритма, перекладывая ответственность за некоторые его шаги на подклассы. Паттерн позволяет подклассам переопределять шаги алгоритма, не меняя его общей структуры.

![[Шаблонный метод (Template Method)-1.png]]

##  Проблема

Вы пишете программу для дата-майнинга в офисных документах. Пользователи будут загружать в неё документы в разных форматах (PDF, DOC, CSV), а программа должна извлекать из них полезную информацию.

В первой версии вы ограничились только обработкой DOC-файлов. В следующей версии добавили поддержку CSV. А через месяц прикрутили работу с PDF-документами.

![[Шаблонный метод (Template Method)-2.png]]

Классы дата-майнинга содержат много дублирования.

В какой-то момент вы заметили, что **код всех трёх классов** обработки документов хоть и отличается в части работы с файлами, но **содержат довольно много общего в части самого извлечения данных**. Было бы здорово избавится от повторной реализации алгоритма извлечения данных в каждом из классов.

К тому же остальной код, работающий с объектами этих классов, наполнен условиями, проверяющими тип обработчика перед началом работы. Весь этот код можно упростить, если слить все три класса воедино либо свести их к общему интерфейсу.

##  Решение

Паттерн Шаблонный метод предлагает **разбить алгоритм на последовательность шагов**, описать эти **шаги в отдельных методах** и вызывать их в одном _шаблонном_ методе друг за другом.

Это позволит **подклассам переопределять некоторые шаги** алгоритма, оставляя **без изменений его структуру и остальные шаги**, которые для этого подкласса не так важны.

В нашем примере с дата-майнингом мы можем создать общий базовый класс для всех трёх алгоритмов. Этот класс будет состоять из шаблонного метода, который последовательно вызывает шаги разбора документов.

![[Шаблонный метод (Template Method)-3.png]]

Шаблонный метод разбивает алгоритм на шаги, позволяя подклассам переопределить некоторые из них.

Для начала **шаги шаблонного метода** можно сделать **абстрактными**. Из-за этого все **подклассы** **должны** **реализовать** каждый из шагов по-своему. В нашем случае все подклассы и так содержат реализацию каждого из шагов, поэтому ничего дополнительно делать не нужно.

>[!note]
>Теперь мы можем определить **общее для всех классов поведение и вынести его в суперкласс**. В нашем примере шаги открытия, считывания и закрытия могут отличаться для разных типов документов, поэтому останутся абстрактными. А вот одинаковый для всех типов документов код обработки данных переедет в базовый класс.

Как видите, у нас получилось два вида шагов: _абстрактные_, которые каждый подкласс обязательно должен реализовать и шаги _с реализацией по умолчанию_ (можно переопределять в подклассах, но не обязательно).

Но есть и третий тип шагов — _хуки_: их не обязательно переопределять, но они не содержат никакого кода, выглядя как обычные методы. Шаблонный метод останется рабочим, даже если ни один подкласс не переопределит такой хук. Однако, **хук даёт подклассам дополнительные точки «вклинивания» в шаблонный метод.**

##  Аналогия из жизни

![[Шаблонный метод (Template Method)-4.png]]
>[!example]+
Проект типового дома могут немного изменить по желанию клиента.
>
Строители используют подход, похожий на шаблонный метод при строительстве типовых домов. У них есть основной архитектурный проект, в котором расписаны шаги строительства: заливка фундамента, постройка стен, перекрытие крыши, установка окон и так далее.
>
Но, несмотря на стандартизацию каждого этапа, строители могут вносить небольшие изменения на любом из этапов, чтобы сделать дом чуточку непохожим на другие.

##  Структура

![[Шаблонный метод (Template Method)-5.png|350]]

1. **Абстрактный класс** определяет шаги алгоритма и содержит шаблонный метод, состоящий из вызовов этих шагов. Шаги могут быть как абстрактными, так и содержать реализацию по умолчанию.
2. **Конкретный класс** переопределяет некоторые (или все) шаги алгоритма. Конкретные классы не переопределяют сам шаблонный метод.

##  Когда применять?

- Когда подклассы должны расширять базовый алгоритм, не меняя его структуры.

 Шаблонный метод позволяет подклассам расширять определённые шаги алгоритма через наследование, не меняя при этом структуру алгоритмов, объявленную в базовом классе.

- Когда у вас есть несколько классов, делающих одно и то же с незначительными отличиями. Если вы редактируете один класс, то приходится вносить такие же правки и в остальные классы.

 Паттерн шаблонный метод предлагает создать для похожих классов общий суперкласс и оформить в нём главный алгоритм в виде шагов. Отличающиеся шаги можно переопределить в подклассах.

Это позволит убрать дублирование кода в нескольких классах с похожим поведением, но отличающихся в деталях.

##  Шаги реализации

1. Изучите алгоритм и подумайте, можно ли его разбить на шаги. Прикиньте, какие шаги будут стандартными для всех вариаций алгоритма, а какие — изменяющимися.

2. Создайте **абстрактный базовый класс**. Определите в нём шаблонный метод. Этот метод должен состоять из вызовов шагов алгоритма. Имеет смысл сделать шаблонный метод финальным, чтобы подклассы не могли переопределить его (если ваш язык программирования это позволяет).

3. Добавьте в абстрактный класс методы для каждого из шагов алгоритма. Вы можете сделать эти методы абстрактными или добавить какую-то реализацию по умолчанию. В первом случае все подклассы _должны_ будут реализовать эти методы, а во втором — только если реализация шага в подклассе отличается от стандартной версии.

4. Подумайте о введении в алгоритм хуков. Чаще всего, хуки располагают между основными шагами алгоритма, а также до и после всех шагов.

5. Создайте конкретные классы, унаследовав их от абстрактного класса. Реализуйте в них все недостающие шаги и хуки.
## ![[Преимущества и недостатки Шаблонного метода-1.png]]

## Пример реализации

Предположим, у нас есть базовый класс AbstractClass, который определяет основной шаблон алгоритма с помощью метода templateMethod(), а некоторые этапы алгоритма делегируются абстрактным методам (должны быть реализованы в подклассах:)
```java
// Абстрактный класс, представляющий шаблонный метод
public abstract class AbstractClass {
    // Шаблонный метод
    public final void templateMethod() {
        stepOne();
        stepTwo();
        stepThree();
    }

    // Абстрактные методы, которые должны быть реализованы в подклассах
    protected abstract void stepOne();
    protected abstract void stepTwo();
    protected abstract void stepThree();
}
```
Теперь создадим подкласс ConcreteClass, который наследует абстрактный класс и реализует абстрактные методы:
```java
// Конкретный класс, реализующий шаблонный метод
public class ConcreteClass extends AbstractClass {
    @Override
    protected void stepOne() {
        System.out.println("Выполняется Шаг 1");
    }

    @Override
    protected void stepTwo() {
        System.out.println("Выполняется Шаг 2");
    }

    @Override
    protected void stepThree() {
        System.out.println("Выполняется Шаг 3");
    }
}
```

Теперь можно использовать класс ConcreteClass, вызывая его templateMethod():
```java
public class Main {
    public static void main(String[] args) {
        AbstractClass concreteClass = new ConcreteClass();
        concreteClass.templateMethod();
    }
}

```

В результате, при запуске кода, будет выведено:
```java
Выполняется Шаг 1
Выполняется Шаг 2
Выполняется Шаг 3
```

Как видно, мы создали абстрактный класс AbstractClass с шаблонным методом
templateMethod(), определили базовый порядок выполнения алгоритма, а затем в классе ConcreteClass реализовали конкретные шаги алгоритма, переопределяя абстрактные методы.
