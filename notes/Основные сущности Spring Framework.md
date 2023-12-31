---
tags: Spring
---
[Из каких основных сущностей состоит Spring-приложение? – Java Interview Review](https://itsobes.ru/JavaSobes/iz-kakikh-osnovnykh-sushchnostei-sostoit-spring-prilozhenie/)
![[Spring Под капотом.png]]
**Bean** – объект бизнес-логики в терминологии Spring Framework.

**BeanDefinition** – описание того, как создавать бин. Объект хранит его тип, метаинформацию, набор параметров для конструктора.

**BeanFactory** – главная точка входа в [DI-контейнер](https://itsobes.ru/JavaSobes/kak-spring-framework-realizuet-pattern-dependency-injection). Хранит BeanDefinition-ы, умеет создавать по ним экземпляры бинов, или выдавать существующие, в зависимости от [скоупа](https://itsobes.ru/JavaSobes/kakoi-u-spring-binov-skoup-po-umolchaniiu).
	**BeanPostProcessor** – донастраивает только что созданные бины, перед тем как положить их в контейнер. Его методы уже упоминались в [публикации про жизненный цикл](https://itsobes.ru/JavaSobes/opishite-zhiznennyi-tsikl-spring-bean). Типичное место, чтобы **оборачивать бины в прокси**. Также с помощью такого пост-процессора внедряются `@Autowired`-зависимости. Пост-процессоры бинов живут внутри экземпляра `BeanFactory`.


**BeanFactoryPostProcessor** – донастройка еще не созданных бинов. Тоже пост-**обработчик, но для BeanDefinition**. Обычно используется для модификации параметров или класса, из которых будут строиться бины.

Для создания определений бинов в основном применяются классы и интерфейсы ***BeanDefinitionReader**. Некоторые из них вызываются прямо из контекста приложения, другие реализуют `BeanFactoryPostProcessor`. Один такой пост-процессор, например, отвечает за добавление определений бинов по [аннотациям](https://itsobes.ru/JavaSobes/kakie-otlichiia-mezhdu-component-service-repository-i-controller) `@Component` и `@Configuration`.

## Под капотом
[Spring Framework. Введение](https://javarush.com/groups/posts/1676-spring-framework-vvedenie)

Прежде чем [Spring](https://javarush.com/groups/posts/476-spring-dlja-lenivihkh-osnovih-bazovihe-koncepcii-i-primerih-s-kodom-chastjh-1) начнет работать его необходимо сконфигурировать. Раньше это делали с помощью xml файлов (на некоторых проектах, преимущественно старых, продолжают делать это до сих пор). Вот небольшой пример такого конфигурационного файла:

![[Пример xml описания bean.png]]

## Инициализация контекста
Для того чтобы наша конфигурация была понята Spring’ом существует `XmlBeanDefinitionReader`. Это внутренний компонент Spring’a, который сканирует (парсит) xml и на основе того, что мы там написали создает `BeanDefinition`’ы. `BeanDefinition` – это объект, который хранит в себе информацию о бине. Сюда входит: из какого класса его (бин) надо создать; scope; установлена ли ленивая инициализация; нужно ли перед данным бином инициализировать другой и иные проперти, которые описаны в xml. Все полученные `BeanDefinition`’ы складываются в `HashMap`, в которой идентификатором является имя бина (установленное вами или присвоенное спрингом) и сам `BeanDefinition` объект.

После того, как все `BeanDefinition`’ы созданы на сцену выходит новый герой – `BeanFactory`. Этот объект итерируется по `HashMap’e` с `BeanDefinition`’ами, создает на их основе бины и складывает в IoC контейнер. Здесь есть нюанс, на самом деле, при старте приложения, в IoC контейнер попадут бины, которые имеют scope Singleton (устанавливается по-умолчанию), остальные же создаются, тогда когда они нужны (prototype, request, session). А теперь небольшое отступление, познакомимся с еще одним персонажем.

## BeanPostProcessor
Дело в том, что бин это не обязательно класс бизнес-логики вашего приложения. Бином также называют инфраструктурный бин. Вкратце, инфраструктурный бин это класс, который настраивает бины вашей бизнес-логики (да-да, слишком много бинов). Подробнее о нём я расскажу ниже, но чтобы было чуточку понятнее, что именно BPP настраивает, приведу пример. Всем же знакома аннотация `@Autowired`? Так вот, именно `AutowiredAnnotationBeanPostProcessor` ответственен за то, чтобы все ваши классы были внедрены друг в друга.

## BeanFactory
Зная теперь о BPP, нужно уточнить, что итерируясь по `HashMap`’e с `BeanDefinition`’ами сперва создаются и кладутся отдельно (не в IoC контейнер) все `BeanPostProcessor`’ы. После этого создаются обычные бины нашей бизнес-логики, складываются в IoC-контейнер и начинается их настройка с помощью отдельно отложенных BPP.

А происходит это вот как, каждый BPP имеет 2 метода:
```java
postProcessorBeforeInitialization(Object bean, String beanName); postProcessorAfterInitialization(Object bean, String beanName);
```

Происходит итерация по нашим бинам дважды. В первый раз вызывается метод `postProcessorBeforeInitialization`, а во второй раз вызывается метод `postProcessorAfterInitialization`. Наверняка возник вопрос, зачем нужны два метода, объясняю. Дело в том, что для обработки некоторых аннотаций (таких как `@Transactional`, например) наш бин заменяется proxy классом. Чтобы понять зачем это делается, нужно знать как работает `@Transactional`, а работает это вот как. В метод, помеченный данной аннотацией необходимо налету добавить еще пару строк кода. Как это сделать? Верно, с помощью создания класса proxy, внутри которого и будет добавлен необходимый код в нужный метод. А теперь представим такую ситуацию, у нас есть класс:
```java
class A { 
	@Autowired 
	private SomeClass someClass; 
	
	@Transactional
	public void method() { 
		// модификатор доступа обязательно public 
	} 
}
```

В этом классе 2 аннотации `@Autowired` и `@Transactional`. Обе аннотации обрабатываются разными BPP. Если первым отработает `AutowiredBPP`, то все будет в порядке, но если нет, то мы столкнемся с вот какой проблемой. Дело в том, что когда создается класс proxy, то вся мета-информация теряется. Другими словами, информации об аннотации `@Autowired` в proxy классе не будет, а значит и `AutowiredBPP` не отработает, а значит наше поле `someClass` будет иметь значение `null`, что, скорее всего, приведет к NPE. Также стоит знать, что между вызовами методов `postProcessorBeforeInitialization` и `postProcessorAfterInitialization` происходит вызов `init`-метода, если он есть. Это по-большому счету второй конструктор, но отличие в том, что в этот момент все наши зависимости уже внедрены в класс и мы можем к ним обратиться из `init`-метода.

Итак, еще раз алгоритм инициализации контекста:

1. `XmlBeanDefinitionReader` сканирует наш xml-конфигурационный файл.
2. Создает `BeanDefinition`’ы и кладет их в `HashMap`.
3. Приходит `BeanFactory` и из этой `HashMap` отдельно складывает все `BeanPostProcessor`’ы.
4. Создает из `BeanDefinition`’ов бины и кладет их в IoC-контейнер.
5. Тут приходят BPP и настраивают эти бины с помощью 2х методов.
Готово.

## ApplicationContext

Реализация интерфейса **ApplicationContext** – основное хранилище конфигурации Spring-приложения (или его части). Контекст неизменяем, но может быть целиком перезагружен. Xml-файл конфигурации на старте приложения превращается в объект `*XmlApplicationContext`. Для конфигурации на [аннотациях](https://itsobes.ru/JavaSobes/kakie-otlichiia-mezhdu-component-service-repository-i-controller) создастся `AnnotationConfigApplicationContext`. Контекст выполняет четыре разных обязанности:

**1. DI-контейнер.** `ApplicationContext` функционирует как специальная реализация `BeanFactory`. Он также производит и хранит бины, но, в отличие от обычных фабрик, контексты в приложении составляют иерархию. Определения бинов из дочерних контекстов перекрывают родительские.

**2. Загрузка ресурсов.** Под интерфейсом `ResourceLoader` контекст занимается загрузкой в память приложения файлов, как из [classpath](https://itsobes.ru/JavaSobes/chto-takoe-classpath), так и из остальной файловой системы.

**3. Публикация событий приложения.** Контекст распространяет в приложении «события» – наследники `ApplicationEvent`. Любой бин, которому нужно получать уведомления об этих событиях, просто реализует интерфейс `ApplicationListener`. Таким образом реализуется паттерн [наблюдатель](https://ru.wikipedia.org/wiki/%D0%9D%D0%B0%D0%B1%D0%BB%D1%8E%D0%B4%D0%B0%D1%82%D0%B5%D0%BB%D1%8C_(%D1%88%D0%B0%D0%B1%D0%BB%D0%BE%D0%BD_%D0%BF%D1%80%D0%BE%D0%B5%D0%BA%D1%82%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D1%8F)).

**4. Интернационализация.** По коду, набору аргументов и локали, через интерфейс контекста `MessageSource` можно получать локализованные текстовые сообщения для пользователей.

[[Разница между BeanFactrory и ApplicationContext]]
