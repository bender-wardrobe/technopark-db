Приветствуются подробные ответы с ссылками.

1. ***Что такое ACID?***

[Ссылка на презентацию](https://bozaro.github.io/tech-db-lectures/04/#3).
Если вкратце, то сама абиривиатура расшивровывается как:
- Atomicity — Атомарность
Основная статья: Атомарность
Атомарность гарантирует, что никакая транзакция не будет зафиксирована в системе частично. Будут либо выполнены все её подоперации, либо не выполнено ни одной. Поскольку на практике невозможно одновременно и атомарно выполнить всю последовательность операций внутри транзакции, вводится понятие «отката» (rollback): если транзакцию не удаётся полностью завершить, результаты всех её до сих пор произведённых действий будут отменены и система вернётся во «внешне исходное» состояние — со стороны будет казаться, что транзакции и не было. (Естественно, счётчики, индексы и другие внутренние структуры могут измениться, но, если СУБД запрограммирована без ошибок, это не повлияет на внешнее её поведение.)

- Consistency — Согласованность
Основная статья: Согласованность данных
Транзакция, достигающая своего нормального завершения (EOT — end of transaction, завершение транзакции) и, тем самым, фиксирующая свои результаты, сохраняет согласованность базы данных. Другими словами, каждая успешная транзакция по определению фиксирует только допустимые результаты. Это условие является необходимым для поддержки четвёртого свойства.
Согласованность является более широким понятием. Например, в банковской системе может существовать требование равенства суммы, списываемой с одного счёта, сумме, зачисляемой на другой. Это бизнес-правило и оно не может быть гарантировано только проверками целостности, его должны соблюсти программисты при написании кода транзакций. Если какая-либо транзакция произведёт списание, но не произведёт зачисление, то система останется в некорректном состоянии и свойство согласованности будет нарушено.
Наконец, ещё одно замечание касается того, что в ходе выполнения транзакции согласованность не требуется. В нашем примере, списание и зачисление будут, скорее всего, двумя разными подоперациями и между их выполнением внутри транзакции будет видно несогласованное состояние системы. Однако не нужно забывать, что при выполнении требования изоляции, никаким другим транзакциям эта несогласованность не будет видна. А атомарность гарантирует, что транзакция либо будет полностью завершена, либо ни одна из операций транзакции не будет выполнена. Тем самым эта промежуточная несогласованность является скрытой.

- Isolation — Изолированность
См. также: Уровни изолированности транзакций
Во время выполнения транзакции параллельные транзакции не должны оказывать влияние на её результат. Изолированность — требование дорогое, поэтому в реальных БД существуют режимы, не полностью изолирующие транзакцию (уровни изолированности Repeatable Read и ниже).

- Durability — Устойчивость
Независимо от проблем на нижних уровнях (к примеру, обесточивание системы или сбои в оборудовании) изменения, сделанные успешно завершённой транзакцией, должны остаться сохранёнными после возвращения системы в работу. Другими словами, если пользователь получил подтверждение от системы, что транзакция выполнена, он может быть уверен, что сделанные им изменения не будут отменены из-за какого-либо сбоя.


2. ***Уровни изолированности транзакций.***
![](https://i.imgur.com/XEzaWDX.png)
3. ***Что такое “Потерянное обновление (Lost Update)”?***
![](https://i.imgur.com/ALIdUGt.png)
4. ***Что такое “Грязное чтение (Dirty Read)”?***
![](https://i.imgur.com/UcxABRW.png)
5. ***Что такое “Неповторяющееся чтение (Non-Repeatable Read)”?***
![](https://i.imgur.com/vlW6SFA.png)
6. ***Что такое “Чтение фантомов (Phantom Reads)”?***
![](https://i.imgur.com/q1KpUVY.png)
7. ***Что такое “Аномалии сериализации”?***

Ситуация, когда параллельное выполнение транзакций приводит к результату, невозможному при последовательном выполнении тех же транзакций.

8. ***Что такое MVCC? Для чего нужно и как работает?***

Multiversion concurency control. А-ля Гитхаб только в рамках базы данных. [Ссылка на презентацию](https://bozaro.github.io/tech-db-lectures/04/#23)

9. ***Зачем нужны хранимые процедуры и какие особенности их использования?***

Ну это удобно. Функции а-ля в sql. [Плюсы и минусы](https://bozaro.github.io/tech-db-lectures/04/#42)

10. ***Зачем нужны триггера и какие особенности их использования?***

Они нужны лишь для того, чтобы программист не забыл вызвать какую-то функцию.

11. ***Что такое журнал транзакций? Для чего он нужен и как работает?***

Сложна.

12. ***Зачем нужны индексы?***

Упорядочивание данных. Что приводит к большему перфомансу

13. ***Что такое частичные индексы?***

Индексы только на часть таблицы

14. ***Что такое кластерные индексы?***

Вместо того чтобы делать таблицу соответствия индексов. Мы просто сортируем всю таблицу. Может быть только один на таблицу

15. ***Что такое покрывающие индексы?***

Как правило, основная задача метода доступа — вернуть идентификаторы подходящих строк таблицы, чтобы механизм индексирования мог прочитать из них необходимые данные. Но что, если индекс уже содержит все необходимые для запроса данные? Такой индекс называется покрывающим (covering), и в этом случае оптимизатор может применить исключительно индексное сканирование (Index Only Scan). [Статья на хабре](https://habrahabr.ru/company/postgrespro/blog/326096/)

16. ***В чем разница между B-Tree/Hash/Bitmap индексами?***

b-tree: Обычное дерево. Если меньше, влево идем. Если больше, вправо.

17. ***В чем разница между Merge join/Nested loop join/Hash join?***

В общих словах разницы никакой, но на отдельных наборах данных ресурсоемкость алгоритма и время его выполнения могут сильно варьироваться.

Nested loop - (сильно грубое описание) Пробегаем по таблице 1 и для каждой строки из нее смотрим какие строки из таблицы 2 ей подходят.
Обычно очень быстро дает первые строки результата (если конечно таблица 2 не гигантская, а единственная строка из первой таблицы, которая попадет в результат - последняя). Еще алгоритм, практически не требует дополнительной памяти для промежуточной работы и индекс во второй таблице может сильно помочь.

Hash join - Поскольку сравнивать каждую строку с каждой очень долго придумали шаманские танцы. Придумываем некую очень быстро вычислимую функцию от сравниваемых полей возвращающую результат от 1 до N. Просматриваем все строки таблицы 1 и раскладываем их в N корзин. Просматриваем таблицу 2 и для каждой строки проверяем только строки из подходящей корзины.
Достоинства - Общее количество операций сравнения гораздо меньше.
Недостатки - Можно оптимизировать только операции равенства, ибо для равных значений равны и значения hash функций, но ">" "<" уже не гарантируются. :( Если коллизии (совпадения hash при разных исходных данных) лягут так, что все строки попадут в одну корзину - то только лишняя работа.

Merge join - построен на том, что если множества упорядочены, то просматривать их целиком не обязательно можно последовательно идти по одному - потом по другому, затем опять по первому и так пока не надоест.
Недостатки - данные надо заранее упорядочить.

18. ***План выполнения запросов. EXPLAIN.***

19. ***Нормализация и денормализация данных.***

20. ***Что такое секционирование и для чего оно нужно?***

21. ***Что такое COLLATION?***

[Большая и интересная статья](https://simply.name/ru/pg-lc-collate.html)

22. ***Чем отличаются корелирующие и не коррелирующие подзапросы?***

Корелирующий запрос - подзапрос в SQL запросе

23. ***Что лучше, JOIN или подзапрос? Почему?***

Нет понятия лучше или хуже. Просто в определенных случаях надо использовать Join, а в других подзапрос. Суть в чем. Подзапрос выполняется дословно и выполняется каждый раз на каждый элемент. И это стоит учитывать. В случае же с JOIN, наши данные могут идти как конвеер, обрабатывая строку одним за другим. Но при этом будет обрабатываться весь массив данных. Когда как с помощью подзапроса мы можем обрабатывать только те данные, которые удольтворяют какому-то условию.
Например: [ссылка на слайд](https://bozaro.github.io/tech-db-lectures/06/#9)
Например 2: [ссылка на слайд](https://bozaro.github.io/tech-db-lectures/06/#7)

24. ***Почему использование индекса может замедлить выполнение запроса?***

Будет идти не последовательное чтение, а по указателю.

25. ***Что такое селективность индекса?***

Индексная селективность - отношение числа строк соответствующих конкретному ключевому значению к общему числу строк в индексе. Селективность индекса – это показатель того, сколько строк от общего числа приходится на одно ключевое значение индекса. Построим формулу.
Селективность индекса = ( NUM_ROWS/DISTINCT_KEY) / NUM_ROWS = 1/DISTINCT_KEY
Таким образом, чтобы рассчитать селективность индекса достаточно посмотреть значение DISTINCT_KEYS в динамическом представлении ALL_INDEXES. Селективность чаще вычисляют в процентах. Чем больше этот процент, тем хуже селективность. Селективность хороша, если мало строк имеют одинаковые ключевые значения.

26. ***Какие есть варианты протоколирования запросов?***

Логгирование Postgresql. Причем при логгировании всех запросов ничего хорошего не будет, ибо на каждый запрос будет происходить две записи в ЖД. Поэтому чаще всего делают какое-то условие на логгирование

27. ***Что такие VACUUM?***

VACUUM высвобождает пространство, занимаемое «мёртвыми» кортежами. При обычных операциях Postgres Pro кортежи, удалённые или устаревшие в результате обновления, физически не удаляются из таблицы; они сохраняются в ней, пока не будет выполнена команда VACUUM. Таким образом, периодически необходимо выполнять VACUUM, особенно для часто изменяемых таблиц. [Хорошая дока](https://postgrespro.ru/docs/postgrespro/9.5/sql-vacuum.html).
