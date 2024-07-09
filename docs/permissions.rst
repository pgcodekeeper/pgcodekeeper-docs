==========
Привилегии
==========

Для корректной работы pgCodeKeeper, пользователю, который был использован в настройках соединения :ref:`dbStore`, в зависимости от типа базы данных, могут потребоваться дополнительные привилегии.


PosrgreSQL

Для пользователей в PostgreSQL и Greenplum не требуются дополнительные привилегии. 

При использовании расширения `pg_dbo_timestamp <https://github.com/pgcodekeeper/pg_dbo_timestamp/>`_ пользователь должен иметь доступ на чтение объектов:

::
 GRANT USAGE ON SCHEMA schema_name TO user_name;
 GRANT SELECT ON [schema_name.]dbots_object_timestamps TO user_name;

где **schema_name** - схема, в которой находится расширение

MS SQL

Для получения метаданных читаемых объектов требуются следующие привилегии:

::

 USE <DATABASE_NAME>;
 GRANT VIEW DEFINITION ON DATABASE :: <DATABASE_NAME> TO <USER_NAME>;

ClickHouse

Для получения метаданных читаемых объектов требуются следующие привилегии:

::

 GRANT SHOW on *.* to role_name;
 GRANT SHOW ACCESS on *.* to role_name;