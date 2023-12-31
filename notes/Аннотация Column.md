---
tags: ORM
--- 
При использовании аннотации `@Column`, вы можете определить различные атрибуты, такие как имя столбца, тип данных, ограничения на значения столбца и другие параметры, которые влияют на создание и маппинг столбца в базе данных.

```java
@Entity
public class Product {
    @Id
    private Long id;

    @Column(name = "product_name", length = 100, nullable = false)
    private String name;

    @Column(name = "product_price", precision = 10, scale = 2)
    private double price;

    // Геттеры и сеттеры
    // ...
}
```

В этом примере класс `Product` содержит поля `name` и `price`, которые помечены аннотацией `@Column`.

Атрибут `name` в аннотации `@Column` указывает имя столбца в базе данных, к которому будет маппиться поле. В данном случае, `name` будет маппиться на столбец `product_name`, а `price` будет маппиться на столбец `product_price`.

Атрибут `length` указывает максимальную длину значения столбца. В примере `name` ограничено 100 символами.

Атрибут `nullable` указывает, может ли столбец содержать значение `null` или нет. В данном примере, `name` является обязательным и не может быть `null`.

Атрибуты `precision` и `scale` применяются для числовых полей и определяют точность и масштаб числового значения. В примере `price` имеет точность 10 и масштаб 2, что означает, что поле будет хранить число с 10 цифрами, 2 из которых будут после десятичной точки.

Аннотация `@Column` имеет и другие атрибуты, такие как `unique`, `insertable`, `updatable` и т.д., которые позволяют настраивать различные аспекты маппинга столбца.
	insertable/updatable – можно ли добавлять/изменять данные в колонке (by default = true); 

Аннотацию `@Column` можно также применять к геттерам и сеттерам свойств сущности, если они используются вместо прямого доступа к полям.
