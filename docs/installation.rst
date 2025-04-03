=========
Установка
=========

Для начала необходимо установить `Java SDK <https://adoptium.net/temurin/releases/>`_ версии 17+ для вашей платформы.

Eclipse Marketplace
~~~~~~~~~~~~~~~~~~~

Самый простой путь установить pgCodeKeeper - Eclipse Marketplace : в запущенном экземпляре Eclipse версии 4.19 или выше с установленным Eclipse Marketplace, выбрать **Help > Eclipse Marketplace...**.

В поле поиска ввести: pgCodeKeeper.

Выберите пакет pgCodeKeeper, ознакомьтесь с пользовательским соглашением, нажмите кнопку Finish, и начнется установка последней версии pgCodeKeeper.

.. image:: ../images/marketplace.png

По завершению установки для применения изменений необходимо перезапустить Eclipse.

Запустите Eclipse и выберите **Window > Perspective > Open Perspective > Other > pgCodeKeeper**.

Для установки предыдущих версий pgCodeKeeper перейдите по ссылке https://github.com/pgcodekeeper/pgcodekeeper/releases и скачайте нужную вам версию релиза и установите его вручную. На сайте представлены архивы релизов версии 5.10.5 и выше.

Сайт обновления
~~~~~~~~~~~~~~~

Альтернативный путь установки pgCodeKeeper – сайт обновлений: в запущенном экземпляре Eclipse версии 4.19 или выше, выбрать **Help > Install New Software...** Откроется мастер установки плагинов средствами сайта обновлений.

В поле **Work With** ввести путь к сайту обновлений: http://pgcodekeeper.org/update/.

После нажатия **Enter** в списке ниже появятся доступные для установки плагины.

Выберите пакет pgCodeKeeper, согласитесь с установкой, ознакомьтесь с пользовательским соглашением, нажмите кнопку **Finish** и начнется установка.

.. image:: ../images/package_plugin.png

По завершению установки для применения изменений необходимо перезапустить Eclipse.

Запустите Eclipse и выберите **Window > Perspective > Open Perspective > Other > pgCodeKeeper**.

Установка из архива
~~~~~~~~~~~~~~~~~~~

#. Скачайте файл ``pgCodeKeeper-updatesite_<нужная версия>.zip``.
#. В запущенном приложении **Eclipse** выберите ``Help > Install New Software…``.
#. В появившемся окне **Install** рядом с полем ``Work with`` кликните по кнопке **Add**.
#. В следующем окне **Add Repository** нажмите на кнопку **Archive…**.
#. Выберите ранее скачанный zip-файл ``pgCodeKeeper-updatesite`` и нажмите **Add**.
#. В результате в окне **Install** появится пакет **pgCodeKeeper**. Кликните по нему и нажмите **Next**.
#. После завершения загрузки из zip-файла завершите работу с окном **Install New Software**, кликнув **Finish**.
#. Далее подождите завершения установки pgCodeKeeper и перезагрузите Eclipse.

Самостоятельная сборка pgCodeKeeper
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Ещё одной альтернативой является самостоятельная сборка pgCodeKeeper, которую можно скачать `здесь <https://github.com/pgcodekeeper/pgcodekeeper/releases>`_.

Запуск pgCodeKeeper из отдельной сборки
---------------------------------------

#. Скачайте файл ``pgCodeKeeper-<нужная версия>.zip`` для вашей версии операционной системы.
#. Распакуйте скачанный zip-файл.
#. Запустите приложение ``pgcodekeeper``