---
tags: ORM
--- 
При работе с датами рекомендуется установить определенный часовой пояс для драйвера JDBC. Таким образом, приложение будет независимым от текущего часового пояса системы.

Другой способ – настроить свойство hibernate.jdbc.time_zone в файле свойств Hibernate, который используется для создания фабрики сессий. Таким образом, можно указать
часовой пояс один раз для всего приложения.

## java.sql

Hibernate позволяет отображать различные классы даты/времени из Java в таблицах баз данных. Стандарт SQL определяет три типа даты/времени: 
- DATE представляет календарную дату путем хранения лет, месяцев и дней.
Эквивалентом JDBC является java.sql.Date. 
- TIME представляет время дня и хранит часы, минуты и секунды.
Эквивалентом JDBC является java.sql.Time. 
- TIMESTAMP хранит как DATE, так и TIME плюс наносекунды.
Эквивалентом JDBC является java.sql.Timestamp.

Поскольку эти типы соответствуют SQL, их сопоставление относительно простое. Можно использовать [[Аннотация Basic|аннотацию @Basic]] или @Column
![[sql date маппинг.png|400]]
>[!warning]
>Использование типов java.sql для полей сущностей не всегда может быть хорошим выбором. Эти классы специфичны для JDBC и содержат множество устаревших функций.
>
Чтобы избежать зависимостей от пакета java.sql, начали использовать классы даты/времени из пакета **java.util** вместо классов java.sql.Timestamp и java.sql.Time.

## java.util

Точность представления времени составляет одну миллисекунду. Для большинства практических задач этого более чем достаточно, но иногда хочется иметь точность повыше. Поскольку классы в данном API изменяемые (не immutable), использовать их в многопоточной среде нужно с осторожностью. В частности java.util.Date можно признать «эффективно» поток безопасным, если вы не вызываете у него устаревшие методы.
## java.util.Date
Тип java.util.Date содержит информацию о дате и времени с точностью до миллисекунд. Но так как классы из этого пакета не имели прямого соответствия типам данных SQL, приходилось использовать над полями java.util.Date аннотацию **@Temporal**, чтобы дать понять SQL, с каким конкретно типом данных она работает.
![[java util Date маппинг.png|400]]

Для этого у аннотации **@Temporal** нужно было указать параметр **TemporalType**, который принимал одно из трёх значений: DATE, TIME или TIMESTAMP, что позволяло указать базе данных с какими конкретными типами данных она работает.

Тип java.util.Date имеет точность до миллисекунд, и недостаточно точен для обработки SQL-значения Timestamp, который имеет точность вплоть до наносекунд. Поэтому, когда мы извлекаем сущность из базы данных, неудивительно, что в этом поле мы находим экземпляр java.sql.Timestamp, даже если изначально мы сохранили java.util.Date. Но это не страшно, так как Timestamp наследуется от Date.

## java.util.Calendar
Как и в случае java.util.Date, тип java.util.Calendar может быть сопоставлен с различными типами SQL, поэтому мы должны указать их с помощью @Temporal. Разница лишь в том, что Hibernate не поддерживает отображение (маппинг) Calendar на TIME
![[java util Calendar маппинг.png|400]]

## java.time
Начиная с Java 8, доступен новый API даты и времени для работы с временными значениями. Этот API-интерфейс устраняет многие проблемы классов java.util.Date и java.util.Calendar.

Все классы в новом API неизменяемые (immutable) и, как следствие, поток безопасные.
Точность представления времени составляет одну наносекунду, что в миллион раз точнее чем в пакете java.util.

Типы данных из пакета java.time напрямую отображаются (маппятся) на соответствующие типы SQL и поэтому нет необходимости явно указывать аннотацию **@Temporal**: 
- LocalDate соответствует DATE. 
- LocalTime и OffsetTime соответствуют TIME. 
- Instant, LocalDateTime, OffsetDateTime и ZonedDateTime соответств. TIMESTAMP.

Это означает, что можно пометить эти поля только аннотацией @Basic (или @Column), например → 
![[java time маппинг.png|400]]
Каждый временной класс в пакете java.time имеет статический метод parse() для анализа предоставленного значения типа String с использованием соответствующего формата.

Вот как можно установить значения полей сущности:

![[применение java time hibernate.png]]
