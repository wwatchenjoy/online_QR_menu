# 1. Цель проекта
Цель проекта - создание системы онлайн меню, интегрированной с R-Keeper и iiko(далее Система). Гость, приходя в заведение, сможет отсканировать уникальный для каждого стола QR код и получить доступ к меню, с возможностью сделать заказ, добавить в него новые позиции, оплачивать за себя или за несколько Гостей.

# 2. Описание системы
Система состоит из следующих основных функциональных блоков:<br/>
1. Модуль интеграции с R-Keeper или iiko
2. Модуль генерации QR кодов
3. Функционал для 1 гостя
4. Функционал для официанта
5. Функционал для нескольких гостей в сессии
6. Функционал для владельца

## 2.1 Типы пользователей
Система предусматривает четыре типа пользователей: Владелец, один Гость, Сессия Гостей, Официант.<br/>
Гость может сделать заказ, у нескольких Гостей в сессии функционал пополняется возможностью платить только за себя или за всех.<br/>
Владелец получает доступ к аналитике по заведению.<br/>
Официант выполняет свою обычную работу с терминалом R-Keeper или iiko, интегрированным с Системой.

## 2.2 Интеграция с R-Keeper или iiko
R-Keeper и iiko имеют API (на данный момент непонятно насколько они открыты для интеграции)<br/>
https://docs.rkeeper.ru/delivery/vozmoyonosti-api<br/>
https://ru.iiko.help/articles/#!api-documentations/api-newprj<br/>
Весь основной функционал терминалов заведения остается неизменным. Система лишь вносит новые данные, либо запрашивает имеющиеся.<br/>
Система с помощью API должна фактически заменить функцию официанта и автоматически обновлять данные в терминале:<br/>
<br/>
## 2.2.1 На стороне Гостя:
1. Создание заказа
2. Изменение заказа
3. Уведомление о желании оплатить счет<br/>

## 2.2.2 На стороне заведения:
1. Изменения в меню(позиций, цен)
2. Формирование стоп-листов
3. Формирование рекомендаций
4. Уведемление официанта о желании Гостя оплатить счет<br/><br/>
Все изменения в меню проводятся ОФициантом через стандартый терминал(R-Keeper или iiko) в заведении. Весь функционал заведения должен быть синхронизирован внутри Системы и изменять состав меню на стороне Гостя одновременно с изменениями в терминале.<br/>

## 2.2.3 На стороне владельца:
1. Сбор необходимых данных для аналитики
2. Контроль состояния заведения по запросу

## 2.3 Генератор QR кодов
Так как в заведениях зачастую используется нумерация столов, которая позволяет Официантам ориентироваться между заказами Гостей и понимать кому и что нужно принести, то необходимо создать генератор QR кодов, каждый из которых будет являться привязкой конкретного заказа к конкретному столу. Переход по QR коду и подвержение заказа  на странице должны автоматически создавать тикет заказа в терминале(R-Keeper или iiko).

## 2.4 Функционал для одного гостя

### 2.4.1 Оформление заказа
Послеперхода по QR коду и  выбора интересующих позиций, гостю предлагается список Рекомендаций, которыми он может пополнить свой заказ. <br/>
Далее Гость подтверждает свой заказ, после чего тикет на заказ появляется в Системе и отображается в терминале. Официант как и обычно начинает работать с терминалом(R-Keeper или iiko) и приносить позиции из заказа Гостю.<br/>

### 2.4.2 Изменения заказа
У Гостя есть возможность довавить позиции в свой заказ("Заказать еще"), что также изменяет тикет в системе.<br/>

### 2.4.3 Оплата
Когда Гость готов оплатить заказ, ему будет достаточно нажать одну кнопку "Оплатить", после чего на Telegram-бота для Официантов приходит уведомление.

## 2.5 Функционал для официантов
Для реализации функционала уведомления Официанта о подверждении Гостем заказа необходимо создать один _Telegram-бот_ на заведение, в который автоматически приходят уведомления в формате:<br/>
**"Стол №n подтвердил заказ, проверьте терминал"**.<br/>
Уведомления Официанта о желании Гостя оплатить заказ:<br/>
**"Стол №n готов оплатить заказ на сумму k"**.<br/>
После получения уведомления Официант подходит к столу с терминалом для оплаты(как и обычно).

## 2.6 Функционал для нескольких гостей в сессии
Главное отличие одного человека от компании Гостей заключается в записи сразу нескольких заказов за один стол.<br/>
Поэтому в функционал Системы необходимо включить возможность создания Сессий. Сессия - сканирование QR кода несколькими Гостями в один период времени, с созданием в приложении отдельной страницы с группой Гостей.<br/>

### 2.6.1 Сессия
Страница сессии должа генерироваться автоматически после сканирования QR кода несколькими Гостями. На странице должно отображаться:<br/>
1. Количество Гостей в сессии
2. Список Гостей, с указанием суммы заказа каждого Гостя<br/>

Количество Гостей в сессии необходимо указывать небольшом окошке над самим меню, чтобы по количеству иконок можно было убедиться в правильном количестве Гостей в сессии. Пример: <br/>
![Пикча для сессии (1)](https://user-images.githubusercontent.com/71213226/159129872-7802acf6-cb30-410c-8a95-90d1400424aa.png)

При клике на это маленькое окошко, пользователя переводит на страницу со списком всех Гостей, суммой заказа каждого Гостя и общий чек.

### 2.6.2 Способы оплаты в сессии
Суть разграничения способов оплаты заключается в "проблеме больших компаний". Оплата заказа в компании, как правило, обсуждается на уровне слов: либо кто-то один платит за всех, либо каждый сам за себя.<br/>
Но могут возникнуть ситуации, когда в компании хочет уйти один человек, оплатив за себя, а оставшиеся позже оплатят вместе.
Именно поэтому на этапе Оплаты(нажатия кнопки "Оплатить" в интерфейсе Гостя в Системе) пользователю толден предоставляться выбор способа оплаты.
Тут необходимо следовать нескольким условиям:<br/>
1. Первоначально у всех Гостей в сессии установлен способ "Оплатить за себя"
2. При нажатии Гостем кнопки "Оплатить"(установлен способ "Оплатить за себя"), Гость обслуживается Официантом как один и автоматически отключается от сессии остальных Гостей. Отключение конкретного Гостя от сессиии должно быть синхронизированно внутри Системы с остальными Гостями.
3. В случае выбора "Оплатить за всех", автоматически формируется общий чек на всю сессию.<br/><br/>
**Примечание:** Возможнен вариант: сессия из 5 человек, трое хотят заплатить вместе и уйти, а другие два Гостя остаться в заведении и заказать еще. В таком случае для варианта "Оплатить за всех" необходимо сделать функцию выбора Гостей из списка сессии для оплаты одним чеком и их синхронное отключение от сессии после оплаты. Возможные проблемы: нужно внедрять функцию регистрации для кадого Гостя, чтобы не путаться между заказами внутри сессии.

## 2.7 Функционал для владельца
Еще одним важным набором функций является сбор данных для аналитики.<br/>
Так как Система изначально планируется интегрированной с R-Keeper или iiko, то у нее будет доступ ко всем данным из заведения. <br/>
Таким образом необходимо продумать все возможные варианты Аналитики этих данных, которые будут непосредственно удобны для простомра Владельцем заведения.<br/>
Стоит сделать акцент именно на web версию страницу Аналитики и адаптацию под мобильные устройства, а также сделать все пункты(таблички,графики,дашборды) максимально понятными, простыми и, по возможности, красивыми.<br/>
Не исключается возможность использования "коробочных решений" :YANDEX DATALENS, GOOGLE DATA STUDIO, POWER BI.

**Примечание:** на данный момент сбором ТЗ для Аналитики занят Никита.

# 3. Предлагаемый стек технологий
Карт-бланш для Насти и Максима.

# 4. Требования к дизайну
Обсуждаемо.

Логотип и название надо разработать в рамках этого проекта.

Должен присутствовать логотип Системы где-то на странице. 

В нижней части страницы (в подвале) должно быть написано:

«Работает на Open Source» со ссылкой на GitHub проекта.
