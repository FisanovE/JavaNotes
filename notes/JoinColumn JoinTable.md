---
tags: ORM
--- 
## **@JoinColumns**
Используется для группировки нескольких аннотаций **@JoinColumn**, которые используются при установлении связей между сущностями или коллекциями, у которых составной первичный ключ и требуется несколько колонок для указания внешнего ключа.

В каждой аннотации **@JoinColumn** должны быть указаны элементы name и referencedColumnName:
![[JoinColumns аннотация.png|400]]

## @JoinTable
Аннотация **@JoinTable** используется для указания связывающей (сводной, третьей) таблицы между двумя другими таблицами.
![[Join table hibernate аннотация.png]]