=================
Сравнение схем БД
=================

- Открываем мастер сравнения баз данных: **pgCodeKeeper - Diff Wizard / Мастер сравнения** или вызываем контекстное меню pgCodeKeeper проекта и выбираем **pgCodeKeeper - Diff... / Сравнить...**.

.. image:: ../images/diff_wizard_1.png

- Выбираем источник исходной схемы базы данных, которую нужно обновить, и целевой схемы базы данных, на основании которой обновляем исходную базу данных.
- Выбираем кодировки источников и временную зону БД.
- Выбираем тип БД **Database type / Тип базы данных**.
- При необходимости переопределить глобальные настройки можно воспользоваться опцией **Show advanced options / Показать дополнительные опции**.
- Нажимаем кнопку **Next**.

.. image:: ../images/diff_wizard_2.png

В таблице сравнения отмечаем изменения, которые необходимо применить.
Примечание: панель инструментов работает аналогично панели инструментов редактора проектов pgCodeKeeper.

При нажатии кнопки **Finish** откроется SQL редактор со скриптом миграции, сгенерированным для выбранных изменений.