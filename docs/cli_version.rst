.. _cliVersion :

==========
CLI версия
==========

В качестве альтернативы плагина для Eclipse IDE предоставляется возможность сравнения схем баз данных через командную строку. Для этого необходимо скачать pgCodeKeeper-cli приложение, которое работает автономно.

Начало работы
~~~~~~~~~~~~~

Для начала необходимо установить `Java SDK <https://jdk.dev/>`_ для вашей платформы.

Последнюю версию cli сборки можно скачать `здесь <https://github.com/pgcodekeeper/pgcodekeeper/releases>`_. В распакованном архиве использовать файл для передачи параметров: **pgcodekeeper-cli.sh** для Linux систем и **pgcodekeeper-cli.bat** для Windows систем.

Режимы работы
~~~~~~~~~~~~~

Полный набор параметров, доступных для работы с программой Вы можете посмотреть выполнив команду:
::

 ./pgcodekeeper-cli.sh --help

Консольная версия имеет следующие режимы работы:
 - :ref:`diffMode`
 - :ref:`parseMode`
 - :ref:`graphMode`
 - :ref:`insertMode`

.. _diffMode :

diff
""""

Режим по-умолчанию. Сравнивает два источника данных и формирует скрипт миграции. В качестве источника данных может использоваться дамп, проект pgCodeKeeper или база данных.

Пример команды для сравнения двух баз данных PostgreSQL и сохранения скрипта миграции в файл.
::

 ./pgcodekeeper-cli.sh                                                \
 --db-type PG                                                         \
 -o /path/to/file/diff.sql                                            \
 -s 'jdbc:postgresql://127.0.0.1:5432/test_1?user=test&password=test' \
 -t 'jdbc:postgresql://127.0.0.1:5432/test_2?user=test&password=test'

- **--db-type PG** - тип БД.
- **-s <url>** - путь до источника изменений. 
- **-t <url>** - путь до приемника изменений. 
- **- o <path>** - путь до файла, куда будет сохранен скрипт миграции. Без этого параметра результат отобразится в консоли.

.. important:: Сами указатели `-s / --source` и `-t / --target` (не <url>) могут быть опущены, тогда первые два аргумента без указателя будут использоваться в качестве источника и приемника соответственно.

Пример команды для сравнения MS SQL проекта и базы данных и вывода результата в консоль.
::

 ./pgcodekeeper-cli.sh                                                \
 --db-type MS                                                         \
 /path/to/project/                                                    \
 'jdbc:sqlserver://127.0.0.1;databaseName={master};integratedSecurity=true'


Пример команды для сравнения ClickHouse дампа и базы данных и выполнение скрипта на этой же базе данных.
::

 ./pgcodekeeper-cli.sh                                                \
 --db-type CH                                                         \
 -r                                                                   \
 path/to/file/ch_dumpl.sql                                            \
 'jdbc:ch://127.0.0.1:8123/default?user=test&password=test'


Пример вывода результата:
::

    SET search_path = pg_catalog;

    CREATE TABLE public.t2 (
        id integer NOT NULL,
        c2 integer NOT NULL,
        c3 integer NOT NULL
    );

    ALTER TABLE public.t2 OWNER TO test;

.. _parseMode :

parse
"""""

Команда **--parse** позволяет переключить работу в режим сохранения базы данных в виде проекта. Команды, доступные в этом режиме:

- **--update-project** - обновление существующего проекта.

Пример команды для создания нового проекта на основе данной БД.
::

  ./pgcodekeeper-cli.sh                                         /
  --db-type PG                                                  /
  --parse                                                       /
  -o /path/to/empty_rep/                                        /
  'jdbc:postgresql://127.0.0.0:1/test?user=test&password=test'

где

- **--db-type <type>** - тип БД.
- **-o <path>** - указание пути, куда будет сохранен проект.

Пример команды для обновления существующего проекта на основе данной БД.
::

  ./pgcodekeeper-cli.sh                                           /
  --db-type PG                                                    /
  --parse                                                         /
  --update-project                                                /
  -o /path/to/existing_rep/                                       /
  'jdbc:postgresql://0.0.0.0:55001/test?user=test&password=test'

.. _graphMode :

graph
"""""

Команда **--graph** позволяет переключить работу в режим поиска зависимостей объекта. Команды, доступные в этом режиме:

- **--graph-name <object_name>**  - имя искомого объекта, поддерживает регулярные выражения, при отсутствии параметра, будут показаны зависимости всех объектов. Для функций сигнатура является частью имени.
- **--graph-reverse**  - обратное направление поиска зависимостей, с данным параметров выполняется поиск объектов, от которых зависит искомый объект. Без этого параметра выполняется поиск все объектов, которые зависят от искомого.
- **--graph-depth <n>** - глубина поиска зависимостей, по-умолчанию 10.
- **--graph-filter-object <OBJECT_TYPE>** - фильтрация зависимых объектов по типу, отображаются только выбранные типы объектов.
- **--graph-invert-filter** - изменение поведения параметра **--graph-filter-object**, выбранные типы объектов скрываются.

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

- **--db-type <type>** - тип БД.
- **--enable-function-bodies-dependencies** - поиск зависимостей в телах функций.
- **--src-lib-xml <path>** - файл библиотек для проекта.
- **-o <path>** - файл, куда запишется результат, без этого параметра вывод отобразится в консоли.
- **/path/to/rep/** - путь до проекта или url базы данных.

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

.. warning:: В данном режиме геометрические типы оборачиваются в специальные функции с указанием типа геометрии **4326**.

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

- **--db-type <type>** - тип БД.
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