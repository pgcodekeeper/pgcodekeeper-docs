.. _cliVersion :

==========
CLI версия
==========

.. important:: Если вы впервые работаете с консольным pgCodeKeeper настоятельно рекомендуем ознакомиться с данной главой полностью и в прямом порядке.

В качестве альтернативы плагина для Eclipse IDE предоставляется возможность сравнения схем баз данных через командную строку. Для этого необходимо скачать pgCodeKeeper-cli приложение, которое работает автономно.

Начало работы
~~~~~~~~~~~~~

Для начала необходимо установить `Java SDK <https://jdk.dev/>`_ для вашей платформы.

Последнюю версию cli сборки можно скачать `здесь <https://github.com/pgcodekeeper/pgcodekeeper/releases>`_. В распакованном архиве использовать файл для передачи параметров: **pgcodekeeper-cli.sh** для Linux систем и **pgcodekeeper-cli.bat** для Windows систем.

Режимы работы
~~~~~~~~~~~~~

Консольная сборка программы имеет следующие режимы работы:
 - :ref:`diffMode`
 - :ref:`parseMode`
 - :ref:`graphMode`
 - :ref:`insertMode`

Полный набор параметров, доступных для работы с программой Вы можете посмотреть выполнив команду:
::

 ./pgcodekeeper-cli.sh --help


.. _diffMode :

diff
""""

.. important:: Данный режим основной и отрабатывает по умолчанию в отличии от остальных.

Данный режим сравнивает два источника данных(источник и цель) и формирует скрипт миграции. Под источником данных понимиается файл с расширеним **sql** (далее дамп), проект pgCodeKeeper(далее проект) или база данных. Далее рассмотрим работу режима сравнения.

Для базового применения режима **diff** можно использовать следующую команду
::

 ./pgcodekeeper-cli.sh                                             \
 --db-type PG                                                      \ # тип БД
 -s 'jdbc:postgresql://0.0.0.0:55001/test?user=test&password=test' \ # источник
 -t 'jdbc:postgresql://0.0.0.0:55002/test?user=test&password=test' \ # цель
 -o /home/shamsutdinov_er/temp/testDump_3.sql                        # путь до дампа

- **--db-type <type>** - обязательный параметр в который устанавливается тип БД. PG - PostgreSQL b Greenplum, MS - MS SQL или CH - ClickHouse. Этот параметр имеет значение по умолчания PG, что значит при работе с PostgreSQL и Greenplum его можно опустить.
- **-s / --source <path/url>** и **-t / --target <path/url>** - обязательные параметры указывают путь до источников данных если это дамп или проект или же url базы данных как в примере. Сами указатели `-s / --source` и `-t / --target` могут быть опущены тогда приложение возмет за источник первый источник данных а за цель второй источник данных без указателей. Рекомендуем оборачивать в одинарные ковычки url баз данных как в примере.
- **- o / --output <path>** - устанавливается путь и имя до файла дампа. Дамп будет сохранен в том расширениив котором вы это укажите, настоятельно рекомендуем указывать расширение `*.sql`. Вы можите не указывать этот параметр тогда скрипт миграции отобразится в консоли

Источник имеет одну таблицу, цель пустая. Ниже приведен скрипт который был сгенерирован pgCodeKeeper

::

    SET search_path = pg_catalog;

    ALTER SCHEMA public OWNER TO pg_database_owner;
    REVOKE ALL ON SCHEMA public FROM PUBLIC;
    REVOKE ALL ON SCHEMA public FROM pg_database_owner;
    GRANT ALL ON SCHEMA public TO pg_database_owner;

    GRANT USAGE ON SCHEMA public TO PUBLIC;

    CREATE EXTENSION plpgsql SCHEMA pg_catalog;

    COMMENT ON EXTENSION plpgsql IS 'PL/pgSQL procedural language';

    CREATE TABLE public.t2 (
        id integer NOT NULL,
        c2 integer NOT NULL,
        c3 integer NOT NULL
    );

    ALTER TABLE public.t2 OWNER TO test;

Часто используемые парметры с режимом сравнение:

- **-r / --run-on-target** - данный параметр применяет скрипт миграции к источнику данных цель. Может применяться одновременно с параметром **-o /--output**.
- **-R / --run-on <url>** - этот параметр нужно использовать когда мы хотим приминить полученный скрипт миграции к какой-нибудь другой базе данных которая не участвовала в сравнении.

.. _parseMode :

parse
"""""

.. important:: Что бы работать в это режиме обязателено указывать параметры **--parse** и **-o / --output <path>**

Данный режим имеет всего две основные команды ниже приведена комманда которая создает проект pgCodeKeeper в указанной директории с полным соотвествием схеме бд источника.

::

  ./pgcodekeeper-cli.sh                                           /
  --db-type PG                                                    / # тип БД
  --parse                                                         /
  -o /home/shamsutdinov_er/temp/codeKeeperProjs/test/             / # путь
  'jdbc:postgresql://0.0.0.0:55001/test?user=test&password=test'    # бд источник

Если добавить к команде выше параметр **--update-project** то мы актализируем проект в соотвествие с схемой бд источника.

.. _graphMode :

graph
"""""

Команда **--graph** позволяет переключить работу в режим поиска зависимостей объекта. Команды, доступные в этом режиме:

- **--graph-name <object_name>**  - имя искомого объекта, поддерживает регулярные выражения, при отсутствии параметра, будут показаны зависимости всех объектов. Для функций сигнатура является частью имени.
- **--graph-reverse**  - обратное направление поиска зависимостей, с данным параметров выполняется поиск объектов, от которых зависит искомый объект. Без этого параметра выполняется поиск все объектов, которые зависят от искомого.
- **--graph-depth <n>** - глубина поиска зависимостей, по-умолчанию 10.
- **--graph-filter-object <OBJECT_TYPE>** - фильтрация зависимых объектов по типу, отображаются только выбранные типы объектов.
- **--graph-invert-filter** - изменение поведения параметра **--graph-filter-object**, выбранные типы объектов будут скрываться.

Пример команды для поиска зависимостей в локальном проекте:
::

 ./pgcodekeeper-cli.sh
 --db-type PG                               \
 --graph                                    \
 --graph-name public.t1                     \
 --graph-name public.t2                     \
 --graph-name 'public\.f1\(.*'              \
 --graph-depth 2                            \
 --graph-filter-object FUNCTION             \
 --enable-function-bodies-dependencies      \
 --src-lib-xml /path/to/rep/.dependencies   \
 -o result.txt                              \
 /path/to/rep/

где

- **--db-type** - тип БД.
- **--enable-function-bodies-dependencies** - поиск зависимостей в телах функций.
- **--src-lib-xml <path>** - файл библиотек для проекта.
- **-o <path>** - файл, куда запишется результат, без этого параметра вывод отобразится в консоли.
- **/path/to/rep/** - путь до проекта или можно указать url базы данных.

Пример команды для поиска зависимостей в MS SQL базе:
::

 pgcodekeeper-cli.bat                       \
 --db-type MS                               \
 --graph                                    \
 --graph-name \\[dbo\\].\\[TABLE_1\\]       \
 --graph-name \\[dbo\\].\\[TABLE_2\\]       \
 --graph-name '.*TABLE_3.*'                 \
 --graph-depth 2                            \
 --graph-filter-object FUNCTION             \
 --enable-function-bodies-dependencies      \
 -o result.txt                              \
 'jdbc:sqlserver://127.0.0.1;databaseName={master};integratedSecurity=true'


Пример вывода результата:
::

    TABLE public.t1
        COLUMN public.t1.id
            FUNCTION public.f1(bigint)
                VIEW public.v1
                VIEW public.v2
                    VIEW public.v3
                    VIEW public.v4
                        VIEW public.v5
        FUNCTION public.f2()
        VIEW public.v6
        VIEW public.v7
    TABLE public.t2
        VIEW public.v8

.. _insertMode :

insert
""""""

Команда **--insert** позволяет переключить работу в режим сбора данных из базы источника с учетом зависимостей по FK. Команды, доступные в этом режиме:

- **--insert-name <table_name>** - имя таблицы, для который собираются данные.
- **--insert-filter <filter>** - условие по которому будут собираться данные из первоначальной таблицы.

Пример команды для сбора данных в локальный файл:
::

  ./pgcodekeeper-cli.sh                                              /
  --db-type PG                                                       /
  --insert                                                           /
  --insert-name public.t1                                            /
  --insert-filter 'id = 1'                                           /
  -X                                                                 /
  -o result.txt                                                      /
  'jdbc:postgresql://127.0.0.1:5432/test?user=test&password=test'

где

- **--db-type** - тип БД.
- **-X** - оборачивает сгенерированный скрипт в транзакцию.
- **-o <path>** - файл, куда запишется результат, без этого параметра вывод отобразится в консоли.

Пример команды для сбора данных и выполнения скрипта в БД:
::

  ./pgcodekeeper-cli.sh                                                        /
  --db-type MS                                                                 /
  --insert                                                                     /
  --insert-name '[dbo].[TABLE_1]'                                              /
  --insert-filter 'id = 1'                                                     /
  -X                                                                           /
  -R 'jdbc:sqlserver://127.0.0.1;databaseName=testdb;user=user;password=pass'  /
  'jdbc:sqlserver://127.0.0.1;databaseName={master};integratedSecurity=true'

где

- **-R <url>** - база данных, в которой будет выполнен полученный скрипт.

Пример вывода результата:

::
   START TRANSACTION;

   INSERT INTO public.t2 (id, c2, c3)
   VALUES (1, NULL, 1)
   ON CONFLICT DO NOTHING;

   INSERT INTO public.t1 (id, c2, c3)
   VALUES (1, 1, 1)
   ON CONFLICT DO NOTHING;

   UPDATE public.t2 SET c2 = 1 WHERE id = 1;

   COMMIT TRANSACTION;


vmargs
~~~~~~
Все параметры после специального параметра **-vmargs** будут переданы VM.

Использование с ограничениями потребляемой памяти:

::

 ./pgcodekeeper-cli.sh 1.sql 2.sql -vmargs -Xms256m -Xmx2g

Параметр VM *ru.taximaxim.codekeeper.parser.poolsize* позволяет указать количество потоков парсера:

::

 ./pgcodekeeper-cli.sh 1.sql 2.sql -vmargs -Dru.taximaxim.codekeeper.parser.poolsize=5