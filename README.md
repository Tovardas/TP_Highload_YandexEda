# TP_Highload_YandexEda
## 1. Тема и целевая аудитория
### Тема

Яндекс Еда - сервис заказа еды из кафе, ресторанов, продуктовых магазинов

### Целевая аудитория

 Каждый месяц Яндекс Едой пользуется 15 млн пользователей в более чем 400 городах[^1]. Количество пользователей в день нигде не раскрывается, но на основе данных к концу 2020 года - к началу 2021 с 250 тыс заказами в день и кратным ростом ежемесячной аудитории, можно сделать вывод, что кол-во ежедневных пользователей так же возросло и превысило 1 млн[^2].
 
### Аналоги

- Сбермаркет
- Самокат
- ВкусВилл
 
### Страны, в которых работает сервис

+ Россия 
+ Беларусь 
+ Казахстан 
+ Узбекистан 
+ Армения

### Ключевой функционал

- Регистрация и авторизация
- Просмотр ассортеминта ресторанов/магазинов
- Написание и чтение отзыва с оценкой
- Просмотр рейтинга с выбором источника оценок (Яндекс Еда или Яндекс Карты)
- Поиск
- Определение геопозиции клиента/курьера
- Выкладывать фото, видео и описание блюд/товаров/ресторанов


### Ключевые продуктовые решения

- Отслеживание статуса заказа и местоположения курьера в реальном времени
- Поиск объединяет товары, рестораны и блюда
- Разбиение зоны доставки на зоны с точками питания

## 2. Расчет нагрузки

- MAU - 15 млн [^1]
- DAU - 3 млн (данные, которые были взяты из предположения или примерные величины, полученные опытным путем)
- Среднее время использования приложения за день - 45 минут (учитывая составление заказа + отслеживание курьерской доставки)
- Количество заказов в день - 1 млн 
- За последний год в "Яндекс Еда" регистрировалось более 5 тысяч новых пользователей каждый день
- Коэффициент соотношения пикового трафика к среднему k= 2.3 (рассчитано на основе [^3])
- За 5 минут постоянно листая в среднем получается 500 КБ на все запросы для просмотра ленты (Примерно)
- Пользователь в среденем комментирует каждый 5ый заказ (тк значительная часть пользователей не осталвяют отзывы)

### Продуктовые метрики
#### Рассчет в штуках данных 
Личные данные: 1 запись. (в год)
История заказов: 8 (в неделю)
Платёжные данные: 1 штука. (в год)
Отзывы: 2 (в неделю)

Кол-во ресторанов - 420 [^1]
Кол-во позиций в ресторане - 73

#### Рассчет в байтах данных 

- Личные данные: 1 КБ.
- История заказов: 8 заказов по 1,5 КБ =12 КБ.
- Платёжные данные: 0,5 КБ.
- Отзывы: 2 КБ.

- Размер фото (средний) - 16 Кб
- Размер текстовой информации - 1Кб

Итого за 5 лет:
На одного пользователя:
- Личные данные: 5 записей - 5 КБ
- История заказов: 2080 - 3120 КБ
- Платёжные данные: 5 - 5КБ
- Отзывы: 520 - 520кб
- Сумма: 3650 КБ
На один ресторан(учитывая, что половина позиций меняется за год):
- Фото: 16 * 2,5 * 73 = 2920 КБ
- Текстовая информация: 185 КБ (Аналогично)
- Сумма: 3105 КБ
На всех пользователей:
15 млн * 3650 КБ = 51 ТБ
На все рестораны:
3105 КБ * 420 = 1.3 ТБ
#### Сетевой трафик
Типы трафика(приходящего):
- Запросы на получение списка ресторанов (сортировка, фильтры): каждый запрос примерно 5-10 КБ 
- Запросы на оформление заказов: каждый запрос занимает около 10-15 КБ, включая детали заказа, подтверждение и геоданные
- Запросы на оплату: порядка 5-10 КБ на каждый платёжный запрос
- Отправка push-уведомлений: 0,5-1 КБ на уведомление
Пиковое значение в течение суток (12:00 и 20:00) [^3]
200 тысяч заказов, то есть (10+15+10+1)КБ * 200 тыс = 60 Гигабит/час (примерно) = 
Также нужно учесть пользователей, которые не делают заказ или не оплатили его, это еще около:
200 тыс * 10КБ * 5КБ (нет трафика на оплату, и половина останалвивается на составлении корзины) = 81 Гигабит/час

Тогда суточное потребление составит (280 в пиковые часы + около 500 в остальное время) 780 Гигабит

#### RPS

| Действие                       | Средний RPS         | Пиковое RPS                   |
|--------------------------------|---------------------|-------------------------------|
| Запрос на получение ресторанов | 347                 | 798.1                         | 
| Запрос на получение меню       | 312                 | 717.6                         |
| Запрос на оформление заказов   | 14                  | 32.2                          | 
| Запрос на оплату               | 12                  | 27.6                          | 

## 3. Глобальная балансировка нагрузки
### Географическое расположение дата-центров
![img.png](image.png)

Расположение дата-центров:

На основе этих данных целесообразно расположить дата-центры в крупных городах, чтобы обеспечить минимальное время отклика для пользователей и стабильную работу сервиса.

**Россия:** Дата-центры стоит расположить в крупных городах с распределением по регионам страны:
- Москва (европейская часть России)
- Санкт-Петербург (северо-западная часть России)
- Новосибирск (сибирский регион)
- Владивосток (дальний восток)
  
**Беларусь:**
- Минск (покрытие основной части страны)

**Казахстан:**
- Астана (центр страны)
- Алматы (южная часть Казахстана)

**Узбекистан:**
- Ташкент (основной город для покрытия региона)

**Армения:**
- Ереван (главный центр для обслуживания пользователей в регионе)

#### DNS
Для балансировки трафика на уровне DNS будем использовать Latency-based DNS. Этот механизм позволяет направлять пользователей на ближайший дата-центр с минимальной задержкой (RTT), что обеспечивает высокую производительность и доступность сервиса.

#### BGP
Используем BGP Anycast для присвоения одного IP-адреса нескольким дата-центрам в пределах одной страны. Это позволяет пользователям автоматически направляться к ближайшему по расположению дата-центру, что минимизирует задержки и повышает отказоустойчивость сети.

## 4.Локальная балансировка нагрузки

В нашей схеме балансировки запросы будут поступать на балансировщик уровня L3 (сетевой уровень), который будет быстро распределять трафик между несколькими L7 балансировщиками (уровень приложений). Эти балансировщики L7 будут равномерно распределять запросы по конечным серверам, учитывая особенности запросов, такие как заголовки HTTP, куки или параметры маршрутизации.

#### L3 балансировка

L3 балансировщик распределяет трафик на основе IP-адресов, что позволяет быстро и эффективно перенаправлять запросы на нужные региональные центры обработки данных. Это минимизирует задержки и снижает нагрузку на ближайшие серверы

На сетевом уровне будем использовать Virtual Server с IP Hashing для распределения запросов. Этот метод использует хеширование IP-адреса клиента для того, чтобы направлять запросы на один и тот же сервер при повторных обращениях. Это особенно полезно для поддержания сессий и минимизации переключений между серверами.

Будем использовать Keepalived для мониторинга доступности нод. Keepalived будет следить за состоянием серверов и оповещать балансировщик в случае падения или восстановления ноды, что обеспечит отказоустойчивость системы.

#### L7 балансировка

Будем использовать балансировку с помощью Nginx в роли прокси-сервера. Nginx будет принимать входящие HTTP-запросы от клиентов и распределять их между различными серверами-бэкендами на основе URL или содержимого запросов. Таким образом, обеспечивается равномерное распределение нагрузки, оптимизация производительности и отказоустойчивость системы, что особенно важно при высоких пиковых нагрузках

## 5. Логическая схема БД

![Логическая БД.png](Логическая%20БД%20DZ5.png)

## 6. Физическая схема БД

![Логическая БД.png](Логическая%20БД%20DZ5.png)

postgreSQL

![image](https://github.com/user-attachments/assets/6a02d1dc-3a3a-4761-9fa1-01ad3edb9894)

Kafka

![image](https://github.com/user-attachments/assets/1b1c1654-e062-4f0a-b4fd-4488d0a98c1b)

Redis

![image](https://github.com/user-attachments/assets/5845b469-c1ae-4821-81c1-07805e6df1f3)

Amazon S3

![image](https://github.com/user-attachments/assets/ec619d4d-113f-48c8-be61-b4c29ebdbd77)

ClickHouse

![image](https://github.com/user-attachments/assets/fdee2992-c163-4f10-9cbf-915273dbd9e6)

EllasticSearch

Таблицы _Products и _Stores

### Основные решения
**Выбор СУБД:**
* postgreSQL - для основных сущностей
* Redis - для таблиц с временными данными и необходимостью быстрого доступа к информации
* Amazon S3 - для хранения фото 
* ClickHouse - для хранения статистики магазинов
* Kafka - для обработки асинхронных задач и обмена сообщениями между сервисами

**Денормализация:**
* хранение рейтинга  магазина, хотя его можно вычеслить на основе Reviews
* Хранение Категорий в отедльной таблице, вместо enum типа

**Индексы:**
* Уникальный индекс для поля email таблицы users
  * Данный индекс необходим для регистрации/авторизации, когда нужно проверить, есть ли такой email в БД.
* Индекс на поле user_id таблицы Orders:
  * Для быстрого поиска всех заказов, сделанных конкретным пользователем.
* Индекс на поле category_id таблицы Products
  * Для ускорения поиска продуктов по категориям.
* Индекс на поле name таблицы Products
  * Для ускорения поиска продуктов по названию.
* Индекс на поле name таблицы Stores
  * Для ускорения поиска магазинов по названию.
* Индекс на поле store_id таблицы Reviews
  * Для ускорения поиска всех отзывов к одному магазину
* Индекс на поле status таблицы Couriers
  * Для ускорения поиска свобоных курьеров
* Индекс на поле store_id таблицы Products
  * Для ускорения поиска всех продуктов к одному магазину

**Шардирование и резервирование СУБД. Схема резервного копирования:**

Шардинг Корзины по user_id, Отзывы по store_id, Курьеры по delievery_zone, Продукты по store_id, Магазины по delievery_zone, остальные таблицы по id.

В качестве технологии резервного копирования будем использовать полное резервное копирование и инкрементное. Полное резервное копирование будем проводить раз в неделю, а инкрементное будет проходить каждый день. Такая комбинация позволяет минимизировать задержку при сбое основной системы хранения.

## 7. Алгоритмы

**Поиск**

1. Индексация:
В Яндекс Еде каждый ресторан и блюдо из базы данных индексируются по важным полям, таким как название ресторана, , категории, блюда и его описание.
Основные шаги индексации:
Индексация данных: Названия блюд, описания и ключевые слова разбиваются на токены с использованием техники токенизации и индексации n-грамм, что позволяет находить как точные совпадения, так и частичные.
N-граммы: Индексирование на основе последовательностей символов помогает находить совпадения даже по частям слов.
Анализ текста: Алгоритмы анализатора текста создают векторное представление описаний блюд и ресторанов, что поддерживает более эффективный поиск.
2. Поиск (Retrieval):
Когда пользователь вводит запрос, он преобразуется в JSON-объект, который направляется в систему ElasticSearch. В Яндекс Еде поиск осуществляется с учетом как точных совпадений, так и близких по смыслу результатов с помощью алгоритма векторного поиска (ANN).

Векторный поиск:
Запрос пользователя и данные блюд представляются в виде векторов в многомерном пространстве, создаваемых на основе текстовой информации (название, описание) с применением методов вроде Word2Vec.
Векторный поиск с применением алгоритмов ANN позволяет находить ближайшие по смыслу векторы, используя метрики вроде косинусного расстояния. Это важно для нахождения блюд, которые могут не иметь точного совпадения слов, но обладают схожими характеристиками или описаниями.
При запросе "суши с лососем" алгоритм сможет находить блюда с описанием "ролл с рыбой" или "суши с рыбой", поскольку их векторные представления будут находиться близко.
3. Ранжирование (Ranking):
На этапе ранжирования используется алгоритм BM25 для упорядочивания множества найденных блюд и ресторанов по релевантности.

Подробности BM25:
TF (Term Frequency): Определяет, как часто ключевое слово запроса встречается в описании блюда или ресторана.
IDF (Inverse Document Frequency): Определяет, насколько редким является слово в базе данных. Чем реже встречается слово, тем больше его вес.
Длина документа: Описание с большим объемом текста получает меньший вес, что предотвращает искусственное повышение релевантности длинных описаний.
BM25 сортирует результаты на основе релевантности запроса пользователя, учитывая частотные и позиционные характеристики слов в описаниях блюд.

**Распределение нагрузки по предприятиями (Least Connectioins)**

1. Инициализация
Система управления распределением заказов (балансировщик) регулярно собирает и обновляет данные о текущей загрузке всех ресторанов и курьеров в системе. Загрузка определяется числом активных заказов, обрабатываемых каждым предприятием и курьером. Эти данные обновляются в реальном времени и доступны через распределенную базу данных с низкой задержкой, обеспечивая актуальную информацию для принятия решений.

2. Выбор оптимального предприятия
При поступлении нового заказа система проводит выбор оптимального ресторана для его обработки, следуя алгоритму на основе метода наименьшего количества соединений (Least Connections):

Сканирование зоны доставки: Балансировщик определяет список доступных ресторанов, обслуживающих зону, куда необходимо доставить заказ.
Проверка загрузки: Для каждого ресторана в этой зоне проверяется текущее количество активных заказов.
Выбор ресторана: Заказ направляется в ресторан с наименьшей загрузкой (меньшим числом активных заказов). Если два или более ресторанов имеют одинаковую загрузку, система выбирает ресторан, который находится ближе всего к адресу доставки для минимизации времени доставки.
Дополнительные факторы: Если два ресторана также находятся на одинаковом расстоянии, может использоваться дополнительная логика, такая как приоритетность с учетом времени работы, доступности курьеров или других параметров.
3. Обновление статуса
Как только заказ передается выбранному ресторану или назначается курьеру:

Увеличение счетчика: Система мгновенно обновляет счетчик активных заказов у данного ресторана и/или курьера.
Мониторинг выполнения: В процессе выполнения заказа система отслеживает его статус в реальном времени, используя данные от ресторанов и курьеров.
Уменьшение счетчика: После завершения заказа (доставка подтверждена) счетчик активных заказов уменьшается, и информация обновляется в системе для дальнейшего использования.
4. Масштабирование и устойчивость
Для обеспечения высокой производительности и надежности в крупном приложении применяются следующие подходы:

Горизонтальное масштабирование: Система балансировки нагрузки реализована на распределенных серверах, которые могут добавляться по мере роста нагрузки.
Кеширование данных: Для уменьшения нагрузки на основную базу данных информация о текущей загрузке ресторанов и курьеров может кешироваться с частыми обновлениями, что позволяет быстро принимать решения о распределении новых заказов.
Резервирование: Использование резервных механизмов обработки позволяет быстро переключиться на альтернативные рестораны или курьеров при сбоях или задержках.
Обработка с учетом пиков: В периоды пиковой нагрузки система может использовать динамическое распределение с учетом заранее установленных коэффициентов, чтобы равномернее распределять заказы.
5. Мониторинг и аналитика
Система включает инструменты мониторинга и аналитики, позволяющие отслеживать эффективность алгоритма распределения:

Метрики нагрузки: Сбор и анализ данных о средней загрузке ресторанов и курьеров, времени обработки заказов и проценте успешных доставок.
Оптимизация: Постоянная настройка алгоритма распределения на основе исторических данных и аналитики для повышения производительности и сокращения времени доставки.

**Подбор курьеров**

Для хранения информации о курьерах в Яндекс Еде используется геопространственный индекс, реализованный с помощью PostGIS. Город разделен на зоны доставки, каждая из которых представлена в виде полигона, сохраненного в базе данных. Курьеры отправляют свою геолокацию каждые 3-4 секунды, данные фиксируются в виде точек с координатами широты и долготы. На основе этих координат с использованием функций PostGIS определяется, в какой зоне доставки находится курьер.

При поступлении нового заказа система выполняет пространственный запрос, чтобы найти курьеров, расположенных в пределах зоны доставки ресторана или ближайших зон. С помощью функции ST_Contains проверяется нахождение курьера внутри полигона зоны, а функция ST_Distance используется для определения расстояния до ресторана, чтобы выбрать ближайшего доступного курьера.

**Распределение курьеров по заказам**

Зная местоположение курьеров, находящихся в зоне доставки, происходит расчет времени курьера в пути (основываясь на сервисе Яндекс Карты, учитывая транспортное средство курьера). Курьеру, чье время в пути окажется наименьшим, предлагается принять заказ. В случае отказа, предложение "принять заказ" показывается второму по времени в пути курьеру. Данный алгоритм не обеспечивает оптимальное общее время в пути для группы заказов, но из-за разбиения на зоны доставки, курьер прибудет на место вовремя.

## 8 Технологии

| Технология               | Область применения                                                 | Мотивация                                                                                     |
|--------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------|
| **Nginx**                | Балансировка L7, терминация SSL                                    | Легкость в конфигурации, высокая пропускная способность и возможности кэширования             |
| **Kubernetes**           | Деплой и масштабирование                                           | Обеспечивает автоматизацию масштабирования, отказоустойчивость и управление ресурсами          |
| **Kafka**                | Брокер сообщений                                                   | Высокая производительность для обработки больших объемов потоковых данных                      |
| **Amazon S3**            | Хранение статического контента (видео)                             | Облачное хранилище с высокой отказоустойчивостью и масштабируемостью                          |
| **Redis**                | Кэш для авторизационных токенов                                    | Предоставляет сверхбыстрый доступ к данным, используя оперативную память для хранения                      |
| **PostgreSQL**           | Хранение основных данных                                           | Реляционная СУБД с широким набором функциональных возможностей                       |
| **ClickHouse**           | Хранение данных для частых и объемных агрегатов (просмотры, лайки) | Оптимизированная колоночная СУБД, обеспечивающая быструю обработку аналитических запросов      |
| **gRPC**                 | Взаимодействие микросервисов, SQLite-сервер                        | Позволяет эффективно и быстро передавать данные между сервисами, снижая накладные расходы      |
| **Golang**               | Бэкенд, серверная часть                                            | Высокая скорость компиляции и исполнения, а также строгая типизация обеспечивают надежность    |
| **Prometheus + Grafana** | Мониторинг и отображение статистики                                | Эффективное решение для сбора метрик и удобной визуализации данных                            |
| **TypeScript**           | Фронтенд                                                           | Статическая типизация                       |
| **ElasticSearch**	| Поисковый движок	| Мощный движок для полнотекстового поиска, обеспечивающий быстрый и эффективный поиск данных по ключевым словам и заданным фильтрам. |
| **Word2Vec**	| Представление текста в виде векторов |	Сопостовляет понятиям их векторное представление, для поиска по смысловым синонимам |
| **BM25**	| Ранжирование в поиске |	Алгоритм ранжирования, оптимизированный для полнотекстового поиска, который учитывает частоту терминов и длину документа. |
| **React**                | Фронтенд, проектирование интерфейсов                               | Позволяет быстро создавать динамические и масштабируемые интерфейсы благодаря компонентной архитектуре |
| **Swift**                | Фронтенд (iOS)                                                     | Интуитивно понятный и мощный язык для создания нативных приложений под iOS                    |
| **Kotlin**               | Фронтенд (Android)                                                 | Обеспечивает простую и современную разработку Android-приложений                               |
| **Webpack**              | Сборка модулей фронтенда                                           | Позволяет оптимизировать загрузку ресурсов и улучшить производительность SPA                   |
| **GitLab**               | Система контроля версий, CI/CD                                     | Популярная платформа для автоматизации CI/CD процессов и управления версиями                   |
| **PostGis**               | Хранение геоданных в БД                                   | Позволяет хранить геоданные в форме полигонов и проверять вхождение точек в полигоны                 |

## 9. Обеспечение надежности

### Kubernetes
- автоматически перезапускает контейнеры при их сбоях, что обеспечивает стабильность работы сервиса;
- управляет распределением и перераспределением физических ресурсов для поддержания бесперебойного функционирования.

### Nginx
Для повышения отказоустойчивости и уменьшения числа сбоев, Nginx можно развернуть в виде нескольких экземпляров с кластеризацией для распределения нагрузки. При выходе из строя одного узла остальные инстансы Nginx продолжают обрабатывать трафик. Репликация настроек и применение конфигурационного менеджера позволяют централизованно обновлять конфигурацию на всех серверах.

### Безопасность
* Защита данных: шифрование информации и доступы, настроенные по принципу минимально необходимых привилегий.
* Хранение конфиденциальных данных: пароли пользователей хранятся в зашифрованном виде с использованием соли, а остальные секреты сохраняются в vault в зашифрованной форме.
* Обнаружение и реагирование на инциденты: мониторинг системных логов и применение систем для выявления вторжений и подозрительной активности.
* Регулярные проверки безопасности.
  
### Мониторинг и алертинг
- Логирование всех входящих запросов и ответов с присвоением уникального request-id.
- Сбор логов в ClickHouse.
- Парсинг логов с помощью парсера, который по заданным правилам анализирует логи для вычисления метрик, таких как количество определенных ошибок.
- Создание и поддержка пользовательских метрик для Prometheus, например, счетчики запросов на ручки и их тайминги.
- Настройка графиков в Grafana по всем метрикам и установка алертов для уведомления соответствующих команд при аномальных значениях.
Эта система позволяет эффективно искать нужные логи, отслеживать текущее состояние по графикам и оперативно реагировать на аномалии благодаря алертам.

### Отказоустойчивость
- Резервирование ресурсов: дублирование вычислительных мощностей и сетевой инфраструктуры, использование резервных устройств, кластеризация серверов и резервное хранение данных для предотвращения сбоев.
- Репликация баз данных: обеспечивает доступность системы для пользователей даже при сбое узлов или сетевых неполадках.
- Геораспределенные ЦОД: развертывание системы в нескольких географически удаленных центрах обработки данных для обеспечения высокой доступности и устойчивости к различным сбоям, включая природные катастрофы, отключения электроэнергии и сетевые неполадки. Это также помогает сократить задержки в сети и повысить производительность пользователей в разных регионах.
- Gracefull shutdown : обработка активных запросов при завершении работы сервиса.
- Двухфазный коммит: используется для обеспечения консистентности данных при распределенных транзакциях, таких как сохранение данных заказа.
- Redis Sentinel: используется для обеспечения отказоустойчивости Redis. Он включает отдельный следящий узел для мониторинга доступности мастера и управляет репликами при сбое мастера. Sentinel автоматически назначает один из узлов slave в качестве нового мастера и перенастраивает другие узлы для работы с ним. Кроме того, Sentinel проверяет доступность мастера и реплик, отправляет уведомления о проблемах, а при необходимости вносит изменения в конфигурации узлов, обеспечивая автоматическое управление процессом failover.
  
## 10. Схема проекта
![Схема_проекта.png](Схема%20проекта.drawio.png)


## 11. Расчет ресурсов

| RPS | средний | пик |
| ---  | ---- | --- |
| Получение списка ресторанов | 347  | 798 |
| Получение меню ресторана | 312  | 717 |
| Поиск | 300  | 603 |
| Написание отзыва | 40  | 92 |
| Просмотр отзывов | 76  | 263 |
| Оформление заказа | 252 | 573  | 
| Оплата | 225  | 515  |
| Проверка авторизации + авторизация | 617 | 1418 |
| Корзина | 90 | 200 |
| Определение текущего местоположения курьеров | 102 | 229 |
| Загрузка новых данных о ресторанах | 30 | 45 |

Объединим RPS в сервисы
| Сервис | пиковый RPS |
| --- | --- | 
| Auth Service | 1418 |
| User Content Service | 1418 | 
| Search Service | 603  |
| Сart Service | 200 |
| Reviews Service | 92 + 263 = 355 |
| GEO Service | 229 |
| Products Service | 717 |
| Stores Service | 818  |
| Ordrers Service | 778  |
| API Gateway | 6536 |
| Суммарный | 13072 |

В исследовании nginx сказано, что сервер с 4 CPU выдерживает 1735 HTTPS CPS, а севрер с 16 CPU выдерживает 6676 HTTPS CPS. Из-за неравномерности нагрузки, предлагается оборудовать сервер в Москве 16 CPU, а в остальных локациях 4 CPU

13072 - 6676 = 6396 т.е.  **1 Сервер по 16 CPU**

6396 / 1735 = 3.69, т.е.  **4 Сервера по 4 CPU** 


Микросервисы:
1 ядро CPU на 50 и 1 ГБ RAM на 500 запросов

| Сервис | CPU | RAM |
| --- | --- | --- | 
| Auth Service | 29 | 3 |
| User Content Service |  29 | 3 |
| Search Service | 13 | 2 |
| Сart Service| 4 | 1 |
| Reviews Service | 8 | 1 |
| GEO Service | 5 | 1 |
| Products Service | 15 | 2 |
| Stores Service | 17 | 2 |
| Ordrers Service | 16 | 2 |
| API Gateway | 131 | 14 |

| Сервис | Конфиг | количество | Приблизительная стоимость | 
| --- | --- | --- | --- |
| Nginx | 16 core CPU; 2x8 GB RAM 2400 MHz DDR4 | 1 | 120 тыс. руб |
| Nginx | 4 core CPU; 1x8 GB RAM 2400 MHz DDR4 | 4 | 47 тыс. руб |
| Auth Service | 4 core CPU; 1х4 GB RAM 2400 MHz DDR4 | 8 | 40 тыс. руб |
| User Service | 4 core CPU; 1х4 GB RAM 2400 MHz DDR4 | 8 | 40 тыс. руб |
| Search Service | 2 core CPU; 1х4 GB RAM 2400 MHz DDR4 | 7 | 27 тыс. руб |
| Сart Service | 1 core CPU; 1х4 GB RAM 2400 MHz DDR4 | 4 | 18 тыс. руб |
| Reviews Service | 2 core CPU; 1х4 GB RAM 2400 MHz DDR4 | 4 | 27 тыс. руб |
| GEO Service | 1 core CPU; 1х4 GB RAM 2400 MHz DDR4 | 5 | 18 тыс. руб |
| Products Service | 2 core CPU; 1х4 GB RAM 2400 MHz DDR4 | 9 | 27 тыс. руб |
| Stores Service | 4 core CPU; 1х4 GB RAM 2400 MHz DDR4 | 5 | 40 тыс. руб |
| Ordrers Service | 4 core CPU; 1х4 GB RAM 2400 MHz DDR4 | 4 | 40 тыс. руб |
| API Gateway | 16 core CPU; 1х4 GB RAM 2400 MHz DDR4 | 9 | 115 тыс. руб |

Суммарная стоимость :
120,000 (Nginx) + 188,000 (4 Nginx) + 320,000 (Auth) + 320,000 (User) + 189,000 (Search) + 72,000 (Cart) + 108,000 (Reviews) + 90,000 (GEO) + 2,646,000 (Products) + 200,000 (Stores) + 160,000 (Orders) + 1,035,000 (API) = 5,623,000 рублей.

### Источники

[^1]: [Что такое Яндекс Еда](https://dev.go.yandex/services/eda)
[^2]: [Битва за еду: как Delivery Club и «Яндекс.Еда» перекраивают рынок во время пандемии](https://www.forbes.ru/biznes/417203-bitva-za-edu-kak-delivery-club-i-yandekseda-perekraivayut-rynok-vo-vremya-pandemii)
[^3]: [Что заказывают в Яндекс Еде?](https://yandex.ru/company/researches/2020/food)
