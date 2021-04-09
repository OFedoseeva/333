⚡ **В БИТ:ERP открыты вакансии разработчиков 1С, [присоединяйся к нашей команде!](https://hh.ru/search/vacancy?text=%D0%91%D0%98%D0%A2%3AERP&area=1&fromSearchLine=false&st=searchVacancy)**
# PinkRabbitMQ library
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](https://github.com/BITERP/PinkRabbitMQ/blob/master/LICENSE)
[![GitHub release](https://img.shields.io/github/release/BITERP/PinkRabbitMQ.svg)](https://github.com/BITERP/PinkRabbitMQ/releases)

[![Sonar bugs](https://code.bit-erp.ru/sonar/api/project_badges/measure?project=PinkRabbitMQ&metric=bugs)](https://code.bit-erp.ru/sonar/dashboard?id=PinkRabbitMQ)
[![Sonar code smells](https://code.bit-erp.ru/sonar/api/project_badges/measure?project=PinkRabbitMQ&metric=code_smells)](https://code.bit-erp.ru/sonar/dashboard?id=PinkRabbitMQ)
[![Sonar dublications](https://code.bit-erp.ru/sonar/api/project_badges/measure?project=PinkRabbitMQ&metric=duplicated_lines_density)](https://code.bit-erp.ru/sonar/dashboard?id=PinkRabbitMQ)
[![Sonar lines of code](https://code.bit-erp.ru/sonar/api/project_badges/measure?project=PinkRabbitMQ&metric=ncloc)](https://code.bit-erp.ru/sonar/dashboard?id=PinkRabbitMQ)
[![Sonar technical debt](https://code.bit-erp.ru/sonar/api/project_badges/measure?project=PinkRabbitMQ&metric=sqale_index)](https://code.bit-erp.ru/sonar/dashboard?id=PinkRabbitMQ)
[![Sonar vulnerables](https://code.bit-erp.ru/sonar/api/project_badges/measure?project=PinkRabbitMQ&metric=vulnerabilities)](https://code.bit-erp.ru/sonar/dashboard?id=PinkRabbitMQ)

# Введение
Внешняя Native API компонента для 1C 8.3, которая реализует методы для работы с сервером Rabbit MQ через TCP IP протокол. Компонента поддерживается командой разработки [БИТ.MDT (Мобильный терминал данных)](https://bit-erp.ru/mdt), [БИТ.Адаптер](https://bit-erp.ru/adapter), БИТ.IIoT (Промышленный интернет вещей) и используется в наших продуктах. Ид компоненты - PinkRabbitMQ. Компонента поставляется для 32 и 64 битной платформы 1С.

## Поддерживаемые системы

* Windows 32 и 64 бит
* Linux 64 бит

## API
Компонента реализует протокол [AMQP 0.9.1](https://www.rabbitmq.com/resources/specs/amqp0-9-1.pdf).
Компонента имеет ряд методов для работы из встроенного языка 1С. Параметры с тегом [НЕ РЕАЛИЗОВАНО] имеют пустую реализацию, но передавать их все равно требуется. Параметры с тегом [НЕОБЯЗАТЕЛЬНЫЙ] можно не передавать. 

### Список свойств комоненты, связанных с сообщением:

* correlationId - строка, чтение и запись
* messageId - строка, чтение и запись
* Type - строка, чтение и запись
* AppId - строка, чтение и запись
* ContentEncoding - строка, чтение и запись
* ContentType - строка, чтение и запись
* UserId - строка, чтение и запись. В данное свойство разрешается передавать только логин пользователя, использованный для установки соединения
* ClusterId - строка, чтение и запись
* Expiration - число строкой, чтение и запись. Передача строки, отличной от числовой, приведет к вызову исключения.
* ReplyTo - строка, чтение и запись

Эти свойства передаются в качестве метаданных вместе с сообщением в RabbitMQ. Их можно прочитать также на стороне получателя - они реинициализируется в момент вызова basicConsumeMessage().

### Список прочих свойств:

* Version - версия компоненты, только чтение

### Список методов:

<b>Connect</b> - устанавливает соединение с сервером RabbitMQ. Таймаут - 5 секунд. Если в течение этого времени сервер недоступен, а также указаны  неверные логин, пароль или vhost, то будет возвращена ошибка, которая может быть получена через метод GetLastError. 

Параметры:
* host - Строка - Адрес сервера RabbitMQ
* port - Число - Порт работы через tcp (обычно 5672 для amqp и 5671 для amqps)
* login - Строка - Имя пользователя
* pwd - Строка - Пароль пользователя
* vhost - Строка - Vhost пользователя
* pingRate - Число - [НЕ РЕАЛИЗОВАНО][НЕОБЯЗАТЕЛЬНЫЙ]. Частота пульса
* secure - Булево - [НЕОБЯЗАТЕЛЬНЫЙ] использовать защищенное соединение по протоколу TLS 1.2. По-умолчанию Ложь. Если Истина, то нужно также передавать соответствующий порт для amqps соединения 

<b>DeclareExchange</b> - Объявить точку обмена

Параметры:
* name - Строка - Имя exchange
* type - Строка - Тип точки обмена. Поддерживаются "direct", "fanout", "topic"
* onlyCheckIfExists - Булево - Не создавать новую, выбросить исключение, если такой точки нет.
* durable - Булево - Сохранять сообщения на диске на случай рестарта RMQ (не рекомендуется)
* autodelete - Булево -  Удалить после того, как от точки будут отвязаны все очереди.
* arguments - Строка - [НЕОБЯЗАТЕЛЬНЫЙ] произвольные свойства в формате Json-объект. Пример: `{"x-message-ttl": 60000}`.

<b>DeleteExchange</b> - Удалить точку обмена

Параметры:
* name - Строка- Имя точки обмена.
* ifunused - Булево - Удаление будет выполнено, только если точка обмена не используется.

<b>DeclareQueue</b> - Объявить очередь

Параметры:
* name -  Строка - Имя объявляемой очереди.
* onlyCheckIfExists  - Булево - Не создавать очередь с таким именем, использовать существующую
* save - Булево - Кешировать сообщения на диске, на случай падения RMQ.
* exclusive - Булево - [НЕ РЕАЛИЗОВАНО]  Только текущее соединение может иметь доступ к этой очереди.
* autodelete - Булево - Удалить очередь если к ней был подключен, а затем отключен читающий клиент.
* maxPriority - Число - [НЕОБЯЗАТЕЛЬНЫЙ] устанавливает максимальное значение приоритета, которое может быть затем установлено для отправляемых сообщений через метод setPriority. Если 0 - то приоритет не используется. По-умолчанию 0.
* arguments - Строка - [НЕОБЯЗАТЕЛЬНЫЙ] произвольные свойства в формате Json-объект. Пример: `{"x-message-ttl": 60000}`. Документация: https://www.rabbitmq.com/queues.html#optional-arguments

Возвращаемое значение:
 - Имя очереди, заданное явно в 1-м параметре.

<b>DeleteQueue</b> - Удаляет очередь с сервера

Параметры:
* name - Строка - Имя очереди
* onlyIfIdle - Булево - Удаление будет выполнено, только если очередь не используется 
* onlyIfEmpty - Булево -  Удаление будет выполнено, только если очередь пуста

<b>BindQueue</b> - Установить связь очереди. Создает маршрут от точки обмена до очереди.

Параметры:
* queue - Строка - Имя очереди
* exchange - Строка - Имя точки обмена
* routingKey - Строка - Rлюч маршрутизации. 
* arguments - Строка - [НЕОБЯЗАТЕЛЬНЫЙ] произвольные свойства в формате Json-объект. Пример: `{"x-match": "all"}`.

<b>UnbindQueue</b> - Отсоединяет очередь от точки обмена.

Параметры:
* queue - Строка - Имя очереди
* exchange - Строка - Имя точки обмена
* routingKey - Строка - Rлюч маршрутизации. 

<b>BasicPublish</b> -  Отправить сообщение

Параметры:
* exchange - Строка - Имя точки в которую отправляется сообщение
* routingKey - Строка - Ключ маршрутизации (см. руководство RMQ)
* message - Строка - Тело сообщения
* livingTime - Число - [НЕ РЕАЛИЗОВАНО]  Время жизни сообщения в миллисекундах
* persist - Булево - Сбрасывать сообщение на диск
* arguments - Строка - [НЕОБЯЗАТЕЛЬНЫЙ] произвольные свойства в формате Json-объект. Пример: `{"x-message-ttl": 60000}`.


<b>BasicAck</b> Отсылает серверу подтверждение (ack), что сообщение обработано и его можно удалить. Подтвердить можно любое прочитанное сообщение.

Параметры:
* messageTag - число - тег сообщения, который возвращается в параметре метода BasicConsumeMessage

<b>BasicReject</b> - Отказывается от последнего полученного сообщения. Работает по принципу Ack, но в обратную сторону.

Параметры:
* messageTag - число - тег сообщения, который возвращается в параметре метода BasicConsumeMessage

<b>BasicConsume</b> - Начать чтение. Регистрирует потребителя сообщений для очереди.

Параметры:
* queue - Строка - Очередь из которой будем читать сообщения.
* consumerId - Строка - [НЕ РЕАЛИЗОВАНО]  имя потребителя. Если не задан, то имя потребителя сгенерирует сервер и вернет из метода
* noConfirm - Булево - [НЕ РЕАЛИЗОВАНО]  не ждать подтверждения обработки. Сообщения будут удалены из очереди сразу после отправки на клиента.
* exclusive - Булево - [НЕ РЕАЛИЗОВАНО]  монопольно захватить очередь 
* selectSize - Число - количество единовременно  считываемых сообщений из очереди в кеш компоненты. Оптимизационный параметр, который влияет на скорость забора сообщений. Рекомендуемый диапазон 100-1000. Нежелательно устанавливать слишком высокие значения, т.к. чтение большого числа накопленных сообщений в очереди может спровоцировать нехватку памяти на клиенте 1С и падение компоненты без вызова исключения.

Возвращаемое значение:
 - Строка. Имя потребителя, сгенерированное сервером или переданное в параметре ИмяПотребителя.

<b>BasicConsumeMessage</b> - Получить сообщение

Параметры:
* consumerId - Строка - [НЕ РЕАЛИЗОВАНО]  Имя зарегистрированного потребителя
* outdata - Строка - Выходной параметр. Тело сообщения.
* messageTag - Число - Выходной параметр. Тег сообщения для подтверждения через метод BasicAck
* timeout - Число - Таймаут ожидания сообщения в миллисекундах. Не рекомендуется ставить параметр слшком низким, т.к. сообщение просто может не успеть прийти из очереди. Рекомендуемый диапазон 3000-60000.

Возвращаемое значение:
 - Булево. Истина, если из очереди прочитано очередное сообщение. Ложь, если очередь пустая.

<b>BasicCancel</b> - Закрывает канал для чтения сообщений

Параметры:
* channelId - Строка - Имя созданного ранее потребителя.


<b>SetPriority</b> -устанавливает приоритет для всех отправляемых сообщений. Если значение равно 0, то приоритет не будет установлен.

Параметры:
* priority - Число - значение в диапазоне разрешенного приоритета для очереди, но не более 255.

<b>GetPriority</b> - получает приоритет для последнего считанного сообщения. Имеет смысл вызывать внутри цикла чтения сообщений методом BasicConsumeMessage

Параметры:
Отсутствуют

Возвращаемое значение:
 - числовое значение приоритета от 0 до 255 для сообщения

<b>GetRoutingKey</b> - получает ключ маршрутизации для последнего считанного сообщения. Имеет смысл вызывать внутри цикла чтения сообщений методом BasicConsumeMessage

Параметры:
Отсутствуют

Возвращаемое значение:
 - Строка - ключ маршрутизации очереди считанного сообщения.

<b>GetHeaders</b> - получает заголовки для последнего считанного сообщения. Имеет смысл вызывать внутри цикла чтения сообщений методом BasicConsumeMessage

Параметры:
Отсутствуют

Возвращаемое значение:
 - Строка - заголовки считанного сообщения в формате объекта json. Пример: `{"customIntHeader": 42}`.

<b>GetLastError</b> - получает информацию о последней ошибке

Параметры отсутствуют

Возвращаемое значение:
 - Строка - Последнее сообщение ошибки


## Правила работы с компонентой из 1С
1. Завершать работу с компонентой посредством обнуления объекта компоненты. Например:

``` bsl
Компонента = Новый("AddIn.BITERP.PRMQMOBILE");
// Работа с компонентой
Компонента = Неопределено;
```
2. Все выходные параметры в методах компоненты передавать как Неопределено или пустая строка, а также обнулять возвращенные значения. <b>Несоблюдение этого простого правила приводит к огромным утечкам памяти!!! </b> Например:

```bsl
Клиент = Новый("AddIn.BITERP.PinkRabbitMQ");
ТекстСообщения = "";
ТегСообщения = 0;
Пока Клиент.BasicConsumeMessage(Потребитель, ТекстСообщения, ТегСообщения, Таймаут) Цикл
    // Работа с компонентой
    // ...
    //
    ТекстСообщения = ""; 
    ТегСообщения = 0; // при каждой итерации очищаем память по указателю, который неявной хранится в этой переменной
КонецЦикла;
```

3. Не вызывать исключения внутри цикла чтения сообщений. Это приводит к потерянной памяти в rphost-е, т.к. внутри компоненты могут остаться закешированные сообщения, которые не попали в алгоритм очистки ссылок.

```bsl
Клиент = Новый("AddIn.BITERP.PinkRabbitMQ");
ВыходнойПараметр = "";
Пока Клиент.BasicConsumeMessage(Потребитель, ВыходнойПараметр, Таймаут) Цикл
    // Работа с компонентой
    Если Клиент.CorrelationId <> "МОЙ_ИД" Тогда
        ВызватьИсключение "Ошибка чтения свойств!"; // ТАК ДЕЛАТЬ СТРОГО НЕ РЕКОМЕНДУЕТСЯ!!!
    КонецЕсли;
    ВыходнойПараметр = "";
КонецЦикла;
```

4. На сервере все исключения компоненты обрабратывать через Попытку ... Иключение с вызовом в секциии исключения метода GetLastError() для получения истинной детальной ошибки. Пример

```bsl
Клиент = Новый("AddIn.BITERP.PinkRabbitMQ");
Попытка
    Клиент.DeclareQueue(ИмяОчереди, Ложь, Ложь, Ложь, Ложь);
Исключение
    ВызватьИсключение Клиент.GetLastError();
КонецПопытки
```

## Примеры

### Подключение внешней компоненты на клиенте
``` bsl
УстановитьВнешнююКомпоненту("ОбщийМакет.PinkRabbitMQ");
ПодключитьВнешнююКомпоненту("ОбщийМакет.PinkRabbitMQ", "BITERP", ТипВнешнейКомпоненты.Native);
Клиент  = Новый("AddIn.BITERP.PinkRabbitMQ");
```

### Создание точки обмена, очереди и их связывание на клиенте
``` bsl
ИмяТочкиОбмена = "data";
ИмяОчереди = "testroute";
ОтправляемоеСообщение = "Test";
ОтветноеСообщение = "";

УстановитьВнешнююКомпоненту("ОбщийМакет.PinkRabbitMQ");
ПодключитьВнешнююКомпоненту("ОбщийМакет.PinkRabbitMQ", "BITERP", ТипВнешнейКомпоненты.Native);
Клиент  = Новый("AddIn.BITERP.PinkRabbitMQ");

Клиент.Connect("127.0.0.1", 5672, "user", "password", "user");
Клиент.DeclareExchange(ИмяТочкиОбмена, "topic", Ложь, Истина, Ложь);
Клиент.DeclareQueue(ИмяОчереди, Ложь, Ложь, Ложь, Ложь);
Клиент.BindQueue(ИмяОчереди, ИмяТочкиОбмена, "#" + ИмяОчереди + "#");
```

### Создание очереди, отправка и получение сообщения на клиенте
``` bsl
    ИмяОчереди = "testroute";
    ОтправляемоеСообщение = "Test";
    ОтветноеСообщение = "";
    ТегСообщения = 0;
    
    УстановитьВнешнююКомпоненту("ОбщийМакет.PinkRabbitMQ");
    ПодключитьВнешнююКомпоненту("ОбщийМакет.PinkRabbitMQ", "BITERP", ТипВнешнейКомпоненты.Native);
    Клиент  = Новый("AddIn.BITERP.PinkRabbitMQ");
    
    Клиент.Connect("127.0.0.1", 5672, "user", "password", "vhost");
    Клиент.DeclareQueue(ИмяОчереди, Ложь, Ложь, Ложь, Ложь);
    Клиент.BasicPublish("", ИмяОчереди, ОтправляемоеСообщение, 0, Ложь);
    Попытка
        Потребитель = Клиент.BasicConsume(ИмяОчереди, "", Истина, Ложь, 0);
        Пока Клиент.BasicConsumeMessage("", ОтветноеСообщение, ТегСообщения, 5000) Цикл
            Клиент.BasicAck(ТегСообщения);
            Сообщить("Успешно! Из очереди прочитано сообщение " + ОтветноеСообщение);
            ОтветноеСообщение = ""; // Обнуляем, чтобы избежать утечку памяти
            ТегСообщения = 0; // Обнуляем, чтобы избежать утечку памяти
        КонецЦикла;
        Клиент.BasicCancel("");
    Исключение
        Сообщить(Клиент.GetLastError());
    КонецПопытки;
```

## Установка в конфигурацию

1. Скачать архив с релизом компоненты.
2. Загрузить в конфигурацию в качестве общего макета с двоичными данными

## Поддержка TLS

Компонента поддерживает соединение по протоколу TLS версии 1.2. Сертификаты сервера верифицируются при подключении, клиентские сертификаты не поддерживаются.
На сервере RabbitMQ необходимо отключить проверку клиентских сертификатов опциями `ssl_options.verify` и `ssl_options.fail_if_no_peer_cert`.
Подробнее про опции можно узнать из официальной документации: https://www.rabbitmq.com/ssl.html#peer-verification-configuration

## Разворачивание окружения разработки на Windows 10 (Рекомендуется)

Вместе с проектом поставляются заранее скомпилированные зависимости в папке libsPrecompiled для 32 и 64 битных платформ в вариантах debug и release. Поэтому для создания окружения разработки нужно сделать только следующее:

1. Установить Visual Studio 2019 (можно Community). При установке выбрать среди обязательных компонентов также WIndows SDK 10 
2. Скачать исходники репозитория данного проекта. 
3. Запустить файл проекта AddIn.sln в корневом каталоге 

## Разворачивание окружения разработки на Windows 10 и самостоятельная компиляция всех зависимостей (Не рекомендуется)

Порядок изложенный ниже требуется, если нужно с нуля собрать весь проект и скомпилировать все зависимые билиотеки. Если это не требуется, то рекомендуется следовать простому порядку разворачивания окружения, приведенному выше.

1. Установить Visual Studio 2019 (можно Community). При установке выбрать среди обязательных компонентов также WIndows SDK 10 
2. Скачать исходники репозиторий данного проекта. 
3. Скачать исходники либы для работы с протоколом раббита AMQP-CPP 4.1.4. https://github.com/CopernicaMarketingSoftware/AMQP-CPP в папку libs
4. Скомпилировать дебажную и релизную dll в папкe libs\AMQP-CPP-4.1.4\build с помощью cmake. 
```
mkdir build
cd build
cmake .. 
cd ..
cmake --build
```
Для компиляции 64-битной release версии либы
* Открыть файл проекта  amqpcpp.sln 
* Выбрать свойства проекта amqcpp => C/C++ => Code generation => Runtime library => Multi-threaded (/MT)
* Скомпилировать проект Release

Для компиляции 32-битной release версии либы

Нужно создать свой проект с нуля на базе проекта 64-битной компоненты, сгенерированного через cmake

5. Скачать исходники либы для работы с сокетом POCO 1.9.0. https://github.com/pocoproject/poco/tree/master в папку libs
6. Скомпилировать дебажную .lib и .dll (debug_shared)  и релизную (release_static_mt) для следующих подпроектов  Foundation и Net. Компиляцию следует выполнять через sln проекты
7. Для дебажного запуска компоненты поместить скомпилированные dll-ки PocoFoundationd64.dll и PocoNet64d.dll в папку 1cv8\8.3.10.2667\bin
8. Запустить файл проекта компоненты NativeRabbitMQClient в каталоге AddIn.sln
10. Проверить линковку зависимостей для заголовков Properties узла проекта -> С/С++ -> General -> Additional include directories (переключить вкладку debug)
- ..\libs\poco-poco-1.9.0-release\Foundation\include;
- ..\libs\poco-poco-1.9.0-release\Net\include;
- ..\libs\AMQP-CPP-4.1.4\include;
11. Проверить линковку зависимостей для папок lib и dll файлов Properties узла проекта -> Linker -> General -> Additional include directories 
- ..\libs\poco-poco-1.9.0-release\lib64;
- ..\libs\AMQP-CPP-4.1.4\build\bin\Debug\;
12. Проверить линкову для lib файлов зависимостей Properties узла проекта -> Linker -> Input -> Additional dependencies 
- amqpcpp.lib;
- PocoFoundationd.lib;
13. Скомпилировать компоненту через проект

## Разворачивание окружения разработки для Linux из-под WIndows 10
1. Установить Visual Studio Community 2019 с последними обновлениями
2. Развернуть Linux system for Windows 10 и подключить VS Studio к своей машине [Туториал](https://devblogs.microsoft.com/cppblog/targeting-windows-subsystem-for-linux-from-visual-studio/)
3. Запустить проект [PinrRabbitMQ для Linux](PinkRabbitMQLinux/PinkRabbitMQLinux.sln)

## Сборка проекта через cmake для Windows 10
```
mkdir build
cd build
cmake .. 
cmake --build . --config Release
```

## Сборка проекта через Visual Studio для Linux

1. Запустить проект [PinrRabbitMQ для Linux](PinkRabbitMQLinux/PinkRabbitMQLinux.sln) 
2. Открыть свойства проекта и в меню  Confugartion properties -> General -> Configuration type выбрать Dynamic library (so)
3. Скомпилировать проект через f5
4. Либа будет скомпилирована в каталоге ~/projects/PinkRabbitMQLinux/bin/x64/debug/ удаленной машины


## Сборка проекта через cmake для Linux
Сборку необходимо производить на Ubuntu 16 или Debian 8.
```sh
# Установить необходимые пакеты
sudo apt install build-essential cmake

# Собрать библиотеку
cd PinkRabbitMQLinux
mkdir build
cd build
cmake .. 
cmake --build .
```

## Лицензия

Лицензировано на условиях MIT. Смотрите файл [LICENSE](LICENSE)

## Используемые сторонние продукты

* [AMQP-CPP](https://github.com/CopernicaMarketingSoftware/AMQP-CPP) - для взаимодействия с RabbitMQ на основе [лицензии Apache](licenses/AMQP-CPP_LICENSE)
* [POCO](https://github.com/pocoproject/poco) - для работы с сокетом по TCP протоколу для windows на основе [лицензии Boost](licenses/POCO_LICENSE)
* [LIBEVENT](https://github.com/libevent/libevent) - для работы с сокетом по TCP протоколу для Linux на основе [3-clause BSD лицензии](https://libevent.org/LICENSE.txt)
