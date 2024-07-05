=================
Структура проекта
=================

Проект **PostgreSQL** имеет следующую структуру:
::

	.
	├─ EXTENSION
	└─ SCHEMA
	   ├─ schema_name
	   ├─ ...
	   └─ another_schema_name
	      ├─ another_schema_name.sql
	      ├─ DOMAIN
	      ├─ FTS_CONFIGURATION
	      ├─ FTS_DICTIONARY
	      ├─ FTS_PARSER
	      ├─ FTS_TEMPLATE
	      ├─ FUNCTION
	      ├─ OPERATOR
	      ├─ PROCEDURE
	      ├─ SEQUENCE
	      ├─ STATISTICS
	      ├─ TABLE
	      │  └─ table_name.sql
	      ├─ TYPE
	      │  └─ type_name.sql
	      └─ VIEW
	         └─ view_name.sql

- schema_name ... another_schema_name - имена схем.
- another_schema_name.sql - обязательный файл с определением схемы, имя должно совпадать с именем директории.
- table_name.sql, type_name.sql, view_name.sql - файлы с определениями объектов. Имена указываются без имени схем.
- все перегруженные функции хранятся в одном файле. Например, функции **public.func(int)** и **public.func(text)** будут расположены в **SCHEMA/public/FUNCTION/func.sql**.
- определения индексов, ограничений, триггеров и правил расположены в файлах родительских объектов. Например, индекс **public.ii** для таблицы **public.t1** будет расположен в **SCHEMA/public/TABLE/t1.sql**.

Проект **MS SQL** содержит следующую структуру:
::

	.
	├─ Assemblies
	├─ Functions
	├─ Security
	│  ├─ Roles
	│  ├─ Schemas
	│  │  └─ schema_name.sql
	│  └─ Users
	├─ Sequences
	├─ Stored Procedures
	├─ Tables
	│  └─ schema_name.table_name.sql
	├─ Types
	│  └─ schema_name.type_name.sql
	└─ Views
	   └─ schema_name.view_name.sql

- schema_name.sql - обязательный файл с определением схемы.
- schema_name.table_name.sql, schema_name.type_name.sql, schema_name.view_name.sql - файлы с определениями объектов. Имена указываются с именем схемы.
- определения индексов, ограничений, триггеров расположены в файлах родительских объектов. Например, индекс **dbo.ii** для таблицы **dbo.t1** будет расположен в **Tables/dbo.t1.sql**.

Проект **ClickHouse** содержит следующую структуру:
::

	.
	├─ DATABASE
	│   └─ database_name
	│       ├─ database_name.sql
	│       ├─ TABLE
	│       │   └─ table_name.sql
	│       ├─ DICTIONARY
	│       │   └─ dictionary_name.sql
	│       └─ VIEW
	│           └─ view_name.sql
	├─ FUNCTION
	│   └─ function_name.sql
	├─ POLICY
	│   └─ policy_name.sql
	├─ USERS
	│   └─ user_name.sql
	└─ ROLE
	    └─ role_name.sql

- database_name.sql - обязательный файл с определением схемы, имя должно совпадать с именем директории.
- table_name.sql, dictionary_name.sql, view_name.sql - файлы с определениями объектов. Имена указываются без имени базы данных в директории соответствующей базы данных.
- function_name.sql, policy_name.sql, user_name.sql, role_name.sql - файлы с определением объектов.
- определение индексов и ограничений расположены в файлах родительских объектов. Например ограничение **check_t1** для таблицы **default.t1** будет рассположен в **DATABASE/default/TABLE/t1.sql**


Кроме этого в проекте могут содержаться следующие директории:

**MIGRATION**. Содержит сохраненные скрипты миграции. Автоматическое сохранение и удаление скриптов миграции в данной директории можно выбрать на странице настроек :ref:`dbUpdate`.

**OVERRIDES**. Содержит файлы переопределения свойств объектов. Внутренняя структура данной директории дублирует структуру проекта. Сохранить переопределения свойств возможно выбрав соответствующую опцию при сохранении объектов в проект. На данный момент поддерживается переопределение привилегий и владельцев объектов базы данных.
