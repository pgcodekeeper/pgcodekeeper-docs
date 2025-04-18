.. _cliVersion :

==========
CLI версия
==========

В качестве альтернативы плагина для Eclipse IDE предоставляется возможность сравнения схем баз данных через командную строку. Для этого необходимо скачать pgCodeKeeper-cli приложение, которое работает автономно.

Начало работы
~~~~~~~~~~~~~

Для начала необходимо установить `Java SDK <https://adoptium.net/temurin/releases/>`_ для вашей платформы.

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
 - :ref:`verifyMode`

.. _diffMode :

diff
""""

Режим по-умолчанию, команда для вызова **--mode DIFF**. Сравнивает два источника данных и формирует скрипт миграции. В качестве источника данных может использоваться дамп, проект pgCodeKeeper или база данных.

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

 pgcodekeeper-cli.bat                                                 \
 --mode DIFF                                                          \
 --db-type MS                                                         \
 /path/to/project/                                                    \
 "jdbc:sqlserver://127.0.0.1;databaseName={master};integratedSecurity=true"


Пример команды для сравнения ClickHouse дампа и базы данных и выполнение скрипта на этой же базе данных.
::

 ./pgcodekeeper-cli.sh                                                \
 --mode DIFF                                                          \
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

Команда **--mode PARSE** позволяет переключить работу в режим сохранения базы данных в виде проекта. Команды, доступные в этом режиме:

- **--update-project** - обновление существующего проекта.

Пример команды для создания нового проекта на основе данной БД.
::

  ./pgcodekeeper-cli.sh                                         /
  --db-type PG                                                  /
  --mode PARSE                                                  /
  -o /path/to/empty_rep/                                        /
  'jdbc:postgresql://127.0.0.0:1/test?user=test&password=test'

где

- **--db-type <type>** - тип БД.
- **-o <path>** - указание пути, куда будет сохранен проект.

Пример команды для обновления существующего проекта на основе данной БД.
::

  ./pgcodekeeper-cli.sh                                           /
  --db-type PG                                                    /
  --mode PARSE                                                    /
  --update-project                                                /
  -o /path/to/existing_rep/                                       /
  'jdbc:postgresql://0.0.0.0:55001/test?user=test&password=test'

.. _graphMode :

graph
"""""

Команда **--mode GRAPH** позволяет переключить работу в режим поиска зависимостей объекта. Команды, доступные в этом режиме:

- **--graph-name <object_name>**  - имя искомого объекта, поддерживает регулярные выражения, при отсутствии параметра, будут показаны зависимости всех объектов. Для функций сигнатура является частью имени.
- **--graph-reverse**  - обратное направление поиска зависимостей, с данным параметром выполняется поиск объектов, от которых зависит искомый объект. Без этого параметра выполняется поиск всех объектов, которые зависят от искомого.
- **--graph-depth <n>** - глубина поиска зависимостей, по-умолчанию 10.
- **--graph-filter-object <OBJECT_TYPE>** - фильтрация зависимых объектов по типу, отображаются только выбранные типы объектов.
- **--graph-invert-filter** - изменение поведения параметра **--graph-filter-object**, выбранные типы объектов скрываются.

Пример команды для поиска зависимостей в локальном проекте:
::

 ./pgcodekeeper-cli.sh
 --db-type PG                               \
 --mode GRAPH                               \
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
 --mode GRAPH                               \
 --graph-name \\[dbo\\].\\[TABLE_1\\]       \
 --graph-name \\[dbo\\].\\[TABLE_2\\]       \
 --graph-name '.*TABLE_3.*'                 \
 --graph-depth 2                            \
 --graph-filter-object FUNCTION             \
 --enable-function-bodies-dependencies      \
 -o result.txt                              \
 "jdbc:sqlserver://127.0.0.1;databaseName={master};integratedSecurity=true"


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

Команда **--mode INSERT** позволяет переключить работу в режим сбора данных из базы источника с учетом зависимостей по FK. Команды, доступные в этом режиме:

- **--insert-name <table_name>** - имя таблицы, для который собираются данные.
- **--insert-filter <filter>** - условие по которому будут собираться данные из первоначальной таблицы.

.. warning:: В данном режиме геометрические типы оборачиваются в специальные функции с указанием типа геометрии **4326**.

Пример команды для сбора данных в локальный файл:
::

  ./pgcodekeeper-cli.sh                                              /
  --db-type PG                                                       /
  --mode INSERT                                                      /
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
  --mode INSERT                                                                /
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


.. _verifyMode :

verify (beta)
"""""""""""""

Команда **--mode VERIFY** позволяет переключить работу в режим верификации SQL кода по набору правил. Команды, доступные в этом режиме:

- **--verify-source <path>** - путь к директории или файлу с исходниками для проверки.
- **--verify-rule-set <path>** - путь к файлу с правилами.

Правила настраиваются с помощью файла с расширением properties, который представляет собой список пар ключ-значение. Список доступных ключей:

.. list-table::
   :widths: 35 10 12 43
   :header-rows: 1

   * - Правило
     - Тип значения
     - Значение по умолчанию
     - Описание
   * - check_quotes_in_table_column
     - boolean
     - false
     - проверка отсутствия кавычек в имени столбца таблицы
   * - check_semicolon_after_simple_sql
     - boolean
     - false
     - проверка символа ; после простого sql-выражения в теле функции
   * - check_space_after_if
     - boolean
     - false
     - проверка наличия пробела после if в теле функции
   * - check_space_on_math
     - boolean
     - false
     - проверка наличия пробелов до и после математических выражений в теле функции
   * - check_space_after_comma
     - boolean
     - false
     - проверка наличия пробела после запятой в теле функции
   * - check_temp_table
     - boolean
     - false
     - проверка наличия созданий не temp таблицы в теле функции
   * - check_indents
     - boolean
     - false
     - проверка отступов в теле функции (в разработке)
   * - indent_size
     - integer
     - 2
     - значение отступа в теле функции, используется с check_indents (в разработке)
   * - cyclomatic_complexity
     - integer
     - -1
     - максимально допустимая вложенность кода в теле функции
   * - max_function_length
     - integer
     - -1
     - максимальная длина тела функции (в строках)
   * - max_function_params
     - integer
     - -1
     - максимальное количество входных параметров функции (без учета OUT параметров)
   * - method_count
     - integer
     - -1
     - максимально допустимое количество выражений в функции
   * - deny_grant
     - list
     - `-`
     - список пользователей, которым запрещено выдавать права
   * - allowed_function_start
     - list
     - `-`
     - список допустимых значений, с которых может начинаться функция и проверка наличия переноса в начале функции

Пример файла с настройками

::

  check_case_without_else = true
  check_quotes_in_table_column = true
  check_semicolon_after_simple_sql = true
  check_space_after_if = true
  check_space_on_math = true
  check_space_after_comma = true
  check_temp_table = true
  cyclomatic_complexity = 5
  max_function_length = 150
  max_function_params = 4
  method_count = 40
  allowed_function_start = $$, $_$, $BODY$
  deny_grant = Public, user0

Пример команды для верификации кода:

::

  ./pgcodekeeper-cli.sh                      \
  --mode verify                              \
  --verify-rule-set rules.properties         \
  --verify-source file1.sql                  \
  --verify-source file2.sql                  \
  --verify-source dir

Пример вывода результата

::

  file1.sql line 1:1 A function have 7 parameters. There should be no more than 4 input parameters.
  file1.sql line 20:5 Using the case block without the else block is not allowed.
  file1.sql line 39:12 Creating only a temporary table is allowed.
  file1.sql line 48:9 There should be no quotation marks in the table column name.
  file1.sql line 49:9 There should be no quotation marks in the table column name.
  file1.sql line 34:7 Warning: Not space after 'if' keyword.
  file1.sql line 42:6 There must be spaces between math expressions.
  file1.sql line 42:7 There must be spaces between math expressions.
  file1.sql line 43:6 There must be spaces between math expressions.
  file1.sql line 43:7 There must be spaces between math expressions.
  file1.sql line 43:8 There must be spaces between math expressions.
  file1.sql line 43:11 There must be spaces between math expressions.
  file1.sql line 43:12 There must be spaces between math expressions.
  file1.sql line 43:16 There must be spaces between math expressions.
  file1.sql line 50:48 Warning: Not space after comma.
  file1.sql line 12:12 Warning: Function body must be start on: [$$, $_$, $body$].
  file1.sql line 1:1 The method has an NCSS line count 47 expressions. There should be no more than 40 expressions.

vmargs
~~~~~~
Все параметры после специального параметра **-vmargs** будут переданы VM.

Использование с ограничениями потребляемой памяти:

::

 ./pgcodekeeper-cli.sh 1.sql 2.sql -vmargs -Xms256m -Xmx2g

Параметр VM *ru.taximaxim.codekeeper.parser.poolsize* позволяет указать количество потоков парсера:

::

 ./pgcodekeeper-cli.sh 1.sql 2.sql -vmargs -Dru.taximaxim.codekeeper.parser.poolsize=5

Параметр VM *-Dlogging.config* позволяет указать файл с пользовательскими настройками логирования.

::

 ./pgcodekeeper-cli.sh 1.sql 2.sql -vmargs -Dlogging.config=home/user/configs/logback.xml


Пример файла конфигурации с логированием debug вывода в файл:

::

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
        <appender name="FILE" class="ch.qos.logback.core.FileAppender">
            <file>${user.home}/.pgcodekeeper-cli/logs/pgcodekeeper-cli.log</file>
            <append>true</append>
            <immediateFlush>true</immediateFlush>
            <encoder>
              <pattern>%d{yyyy-MM-dd HH:mm:ss} [%level] [%logger{0}] - %msg%n</pattern>
            </encoder>
        </appender>

        <root level="DEBUG">
            <appender-ref ref="FILE" />
        </root>
    </configuration>

