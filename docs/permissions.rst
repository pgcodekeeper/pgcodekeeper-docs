==========
Разрешения
==========

Для корректной работы pgCodeKeeper пользователю, который был задан в настройках соединения :ref:`dbStore`, должны быть выданы соответствующие права.

Для пользователей в PostgreSQL и Greenplum не требуются дополнительные права доступа. Eсли вы пользуетесь расширением `pg_dbo_timestamp <https://github.com/pgcodekeeper/pg_dbo_timestamp/>`_ то у пользователя должно быть соответствующее разрешение.

для MS SQL:

для ClickHouse:

::

 GRANT SHOW on *.* to role_name;       / # для всех основных объектов
 GRANT SHOW ACCESS on *.* to role_name;/ # для политик

- Более подробно ознакомиться с видами привилегий можно:
- **PostgreSQL** - https://www.postgresql.org/docs/15/sql-grant.html
- **Greenplum** - https://docs.vmware.com/en/VMware-Greenplum/7/greenplum-database/ref_guide-sql_commands-GRANT.html
-
- **ClickHouse** - https://clickhouse.com/docs/en/sql-reference/statements/grant