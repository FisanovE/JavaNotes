---
tags: Java/Многопоточность
---
Семафор (Semaphore) - это механизм синхронизации, используемый для контроля доступа к ресурсам в многопоточной среде. Он позволяет ограничивать количество потоков, которые могут одновременно получить доступ к определенному ресурсу или критической секции.

Семафор работает следующим образом:

- У семафора есть счетчик, который указывает количество доступных ресурсов или разрешений.
- Когда поток хочет получить доступ к ресурсу, он пытается получить разрешение от семафора.
- Если счетчик семафора больше нуля, поток получает разрешение и счетчик уменьшается на единицу.
- Если счетчик семафора равен нулю, поток блокируется и ждет, пока другой поток не освободит ресурс и увеличит счетчик семафора.
- Когда поток завершает использование ресурса, он возвращает разрешение семафору, увеличивая счетчик на единицу.
## Методы класса Semaphore
В Java семафор реализован с помощью класса `Semaphore` из пакета `java.util.concurrent`. Вот основные методы класса `Semaphore`:

- `acquire()`: Получает разрешение от семафора. Если счетчик семафора больше нуля, поток получает разрешение, и счетчик уменьшается на единицу. Если счетчик равен нулю, поток блокируется до тех пор, пока не станет доступно разрешение.
- `release()`: Возвращает разрешение семафору, увеличивая счетчик на единицу. Если есть ожидающие потоки, один из них будет разблокирован и получит разрешение.
- `tryAcquire()`: Пытается получить разрешение от семафора без блокировки потока. Если счетчик семафора больше нуля, поток получает разрешение и счетчик уменьшается на единицу. Если счетчик равен нулю, метод возвращает `false`, без блокировки потока.
- `tryAcquire(long timeout, TimeUnit unit)`: Пытается получить разрешение от семафора без блокировки потока в течение определенного времени. Если счетчик семафора больше нуля, поток получает разрешение и счетчик уменьшается на единицу. Если счетчик равен нулю, поток блокируется до истечения указанного времени ожидания.
## Пример
```java
import java.util.concurrent.Semaphore;

class SharedResource {
    private static final int MAX_AVAILABLE = 3;
    private final Semaphore semaphore = new Semaphore(MAX_AVAILABLE, true);

    public void useResource() throws InterruptedException {
        semaphore.acquire(); // Если счетчик семафора больша нуля - получаем разрешение
        try {
            // Код, использующий общий ресурс
        } finally {
            semaphore.release(); // Отдаем разрешение обратно
        }
    }
}

```

В этом примере объект `SharedResource` имеет метод `useResource()`, который использует общий ресурс, защищенный семафором. При вызове метода `useResource()` поток сначала запрашивает разрешение у семафора с помощью метода `acquire()`, а затем освобождает разрешение после использования ресурса с помощью метода `release()`. Счетчик семафора уменьшается при каждом получении разрешения и увеличивается при каждом его возврате.
