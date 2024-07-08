=====================================
Руководство пользователя pgCodeKeeper
=====================================

Плагин pgCodeKeeper предназначен для сравнения схем БД PostgreSQL, MS SQL Server, Greenplum, ClickHouse и выборочного применения различий, с учетом возможных зависимостей между объектами схемы.

Работа с pgCodeKeeper осуществляется с помощью проекта. Он представляет собой объекты схемы БД, хранимые в отдельных файлах в иерархической структуре.

С помощью проекта возможно просмотреть структуру объектов-файлов, и создать скрипт наката схемы проекта на БД, или наоборот, модифицировать структуру проекта объектами из какой-либо БД.

Исходный код pgCodeKeeper находится в свободном доступе и доступен на `GitHub <https://github.com/pgcodekeeper/pgcodekeeper>`_.

.. toctree::

   preface

.. toctree::
   :maxdepth: 1
   :caption: Начало работы

   installation
   new_project
   import_project
   project_structure
   convert_project
   editors
   preferences
   properties

.. toctree::
   :maxdepth: 1
   :caption: Задачи

   db_store
   permissions
   project_update
   db_update
   sort
   project_edit
   formatting
   refactoring
   object_search
   diff
   mock_data

.. toctree::
   :maxdepth: 1
   :caption: Дополнительная информация

   white_black_list
   white_black_list_schemas
   cli_version
   windowsauth
   contributors
   about
