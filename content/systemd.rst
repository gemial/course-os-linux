Systemd
#######

:date: 2022-05-17
:summary: Systemctl и его использование
:status: published
:author: Бубен Мария Михайловна, Добронос Милена Александровна, Кононова Дарья Викторовна

Что такое Systemctl?
====================
.. contents:: Содержание
       :depth: 2

**systemctl** — это утилита, отвечающая за проверку и управление системой *systemd* и диспетчером служб. Это набор библиотек управления системой, утилит и демонов, которые функционируют как преемник демона инициализации *System V*. Новые команды *systemctl* оказались весьма полезными при управлении системными службами. Он предоставляет подробную информацию о конкретных службах *systemd* и других службах, которые используются в системе.

Имейте в виду, что в большинстве случаев *systemctl* не даст никаких результатов, если команда была написана неправильно. Однако, если *systemctl* не удалось выполнить задачу, вы получите сообщение об ошибке, указывающее, где именно произошёл сбой.

Управление службами
===================
Что такое служба?
-----------------
В системе *systemd* служба называется **юнитом**. *Юнит* — это любой ресурс, с которым система знает, как действовать и как управлять. Юниты определены в файлах конфигурации, называемыми файлами **Unit-файлами** или **файлами модулей**.

Проверка статуса службы
------------------------
С помощью systemctl мы можем проверить состояние любой службы systemd на управляемой машине. Команда ``status`` предоставляет информацию о службе. Команда также покажет рабочее состояние или сведения о том, почему оно не работает, или если служба была остановлена непреднамеренно.

.. code-block:: bash

       root@server# systemctl status имя_службы.service
       

Если мы подключены к серверу как пользователь без полномочий root, нам придется запускать команды systemctl с помощью ``sudo``.

.. code-block:: bash

       user@server$ sudo systemctl status имя_службы.service


При использовании *systemctl* не обязательно писать ``.service`` в имени службы. По умолчанию *systemctl* будет искать юниты с этим расширением.

.. code-block:: bash

       root@server# systemctl status имя_службы
       
       
Пример вывода: 

.. code-block:: bash

       root@server# systemctl status dbus
       ● dbus.service - D-Bus System Message Bus
              Loaded: loaded (/lib/systemd/system/dbus.service; static)
              Active: active (running) since Mon 2022-05-02 18:55:55 MSK; 29min ago
         TriggeredBy: ● dbus.socket
                Docs: man:dbus-daemon(1)
            Main PID: 1300 (dbus-daemon)
               Tasks: 1 (limit: 18939)
              Memory: 8.6M
                 CPU: 2.281s
              CGroup: /system.slice/dbus.service
                    └─1300 @dbus-daemon --system --address=systemd: --nofork --nopidfile --systemd-activation --syslog-only


Запуск или остановка службы
===========================
Утилита systemctl также может использоваться для запуска или остановки служб systemd.

.. code-block:: bash

       root@server# systemctl start имя_службы
       root@server# systemctl stop имя_службы

Имейте в виду, что для успешного запуска службы, служба не должна быть запущена, а для успешной остановки, она должа быть запущена.

Перезапуск или перезагрузка службы
==================================
Работающую службу можно перезапустить с помощью команды ``restart``, чтобы автоматически остановить и запустить службу.

.. code-block:: bash

       root@server# systemctl restart имя_службы


Иногда нам не нужно перезапускать службу, чтобы применить изменения конфигурации, если таковые были внесены. Вместо этого мы можем использовать команду ``reload``, которая применит любые изменения конфигурации в работающей службе.

.. code-block:: bash

       root@server# systemctl reload имя_службы


Если мы не уверены, какую из двух команд нам следует использовать, есть дополнительная опция ``reload-or-restart``, которая автоматически определит ее для нас.

.. code-block:: bash

       root@server# systemctl reload-or-restart имя_службы


Включить или отключить службу
=============================
Иногда нужно, чтобы при запуске системы службы запускались автоматически. Особенно часто такое встречается на облачных серверах. Для этого используется команда ``enable``

.. code-block:: bash

       root@server# systemctl enable имя_службы


Для отключения функции автозагрузки для службы используется команда ``disable``

.. code-block:: bash

       root@server# systemctl disable имя_службы


Обзор состояния системы
=======================
Выше рассматривались команды *systemctl* для управления отдельными службами. Рассмотрим другие полезные функции.

Что такое Unit-файл?
--------------------
**Unit-файл** — это простой текстовый файл, который содержит информацию о службе, сокете, устройстве, точке монтирования, файле подкачки, разделе, целевом объекте запуска или группе процессов.

Работа с Unit-файлами
---------------------
Список служб
~~~~~~~~~~~~
Команда ``list-units`` отобразит все активные службы systemd на машине. Вывод данной команды похож ``status``, но менее подробный.

.. code-block:: bash

       root@server# systemctl list-units


Вот пример вывода этой команды:

.. code-block:: bash

       UNIT                           LOAD   ACTIVE SUB           DESCRIPTION
       accounts-daemon.service        loaded active running       Accounts Service
       apache2.service                loaded failed failed        The Apache HTTP Server
       dbus.service                   loaded active running       D-Bus System Message Bus
       bluetooth.service              loaded active running       Bluetooth service
       fail2ban.service               loaded active running       Fail2Ban Service
       snapd.service                  loaded active running       Snap Daemon


Рассмотрим значение каждого столбца.

* **UNIT**: Имя модуля *systemd*.
* **LOAD**: Показывает загружен ли файл конфигурации.
* **ACTIVE**: Показывает запущен ли модуль (active) или успешно завершён (failed).
* **SUB**: Более подробное состояние модуля.
* **DESCRIPTION**: Краткое описание модуля и его функций.

Состояния модулей
~~~~~~~~~~~~~~~~~
Как сказано выше, команда ``list-units`` отобразит только активные службы, но чтобы получить список всех служб нужно использовать параметр ``--all``

.. code-block:: bash

       root@server# systemctl list-units -all


При использовании параметра ``--all`` будут выведены все модули, которые были загружены или которые *systemd* пытался загрузить. Также будут показаны неактивные, службы, находящиеся в мёртвом или неисправном состоянии.

Также есть ряд параметров для фильрации вывода. Один из них -- ``--state=``. Он используется для фильтрации состояния в столбцах **LOAD**, **ACTIVE** и **SUB**.

.. code-block:: bash

       root@server# systemctl list-units -all --state=failed


Также можно отфильтровать службы по типу.

.. code-block:: bash

       root@server# systemctl list-units -all --type=mount


Команда ``list-units`` показывает только те модули, которые *systemd* пытался загрузить в память. Для того чтобы посмотреть все модули, включая незагруженные, используется команда ``list-unit-files``. Она отобразит все доступные модули.

.. code-block:: bash

       root@server# systemctl list-unit-files


Пример вывода:

.. code-block:: bash

       UNIT FILE                 STATE           VENDOR PRESET
       apache2.service           enabled         enabled
       apparmor.service          enabled         enabled
       bluetooth.service         enabled         enabled
       dbus.service              static          -


Создание Unit-файла
===================

Иногда нужно создать свой Unit-файл для пользовательских служб или другого экземпляра существующей службы. Для создания Unit-файла нужны права *root*. Файл создаётся в каталоге ``/etc/systemd/system/``. Это делается так:

.. code-block:: bash

       root@server# touch /etc/systemd/system/имя_службы.service
       root@server# chmod 644 /etc/systemd/system/имя_службы.service
       

Далее открываем этот файл в текстовом редакторе (например *Vim* или *Nano*) и пишем в него параметры конфигурации службы. Ниже показан базовый пример Unit-файла.

.. code-block:: bash

       [Unit]
       Description=This is the manually created service
       After=network.target

       [Service]
       ExecStart=/path/to/executable

       [Install]
       WantedBy=multi-user.target


Разберём каждую настройку подробнее.

#. **Description**: Описание службы, которые будет отображаться при использовании команды ``status``.
#. **After**: Указывает на то, что служба запускается только после указанной службы или цели [1]_.
#. **ExecStart**: Путь к исполняемому файлу службы.
#. **WantedBy**: Указывает на цель [1]_, с которой должна запускаться служба.

.. [1] См. `Использование целей`_

После создания Unit-файла нужно сообщить об этом системе.

.. code-block:: bash

       root@server# systemctl daemon-reload
       root@server# systemctl start имя_службы


Просмотр Unit-файла
-------------------
Для вывода Unit-файла конкретного модуля можно использовать команду ``cat``.

.. code-block:: bash

       root@server# systemctl cat имя_службы


Просмотр зависимостей
---------------------
Для отображения дерева зависимостей модуля есть команда ``list-dependencies``

.. code-block:: bash

       root@server# systemctl list-dependencies имя_службы


Пример вывода:

.. code-block:: bash

       root@server# systemctl list-dependencies bluetooth
       bluetooth.service
       ● ├─dbus.socket
       ● ├─system.slice
       ● └─sysinit.target
       ●   ├─apparmor.service
       ●   ├─apparmor.service
       ●   ├─apparmor.service
       ●   ├─blk-availability.service
       ●   ├─dev-hugepages.mount
       ●   ├─dev-mqueue.mount
       ●   ├─keyboard-setup.service
       ●   ├─kmod-static-nodes.service
       ●   ├─lvm2-lvmpolld.socket
       ●   ├─lvm2-monitor.service
       ●   ├─plymouth-read-write.service
       ●   ├─plymouth-start.service
       ●   ├─proc-sys-fs-binfmt_misc.automount
       ●   ├─setvtrgb.service
       ●   ├─sys-fs-fuse-connections.mount
       ●   ├─sys-kernel-config.mount
       ●   ├─sys-kernel-debug.mount
       ●   ├─sys-kernel-tracing.mount
       ○   ├─systemd-ask-password-console.path
       ○   ├─systemd-binfmt.service
       ○   ├─systemd-boot-system-token.service
       ○   ├─systemd-hwdb-update.service
       ●   ├─systemd-journal-flush.service
       ●   ├─systemd-journald.service
       ○   ├─systemd-machine-id-commit.service
       ●   ├─systemd-modules-load.service
       ○   ├─systemd-pstore.service
       ●   ├─systemd-random-seed.service
       ●   ├─systemd-sysctl.service
       ●   ├─systemd-sysusers.service
       ●   ├─systemd-timesyncd.service
       ●   ├─systemd-tmpfiles-setup-dev.service
       ●   ├─systemd-tmpfiles-setup.service
       ●   ├─systemd-udev-trigger.service
       ●   ├─systemd-udevd.service
       ●   ├─systemd-update-utmp.service
       ●   ├─cryptsetup.target
       ●   ├─local-fs.target
       ●   │ ├─-.mount
       ●   │ ├─boot-efi.mount
       ●   │ ├─home.mount
       ○   │ ├─systemd-fsck-root.service
       ●   │ └─systemd-remount-fs.service
       ●   ├─swap.target
       ●   │ └─swapfile.swap
       ●   └─veritysetup.target


Просмотр свойств
----------------
Для просмотра свойств юнита используется команда ``show``

.. code-block:: bash

       root@server# systemctl show имя_службы


Пример вывода:

.. code-block:: bash

       root@server# systemctl show ssh
       Type=notify
       Restart=on-failure
       Type=notify
       Restart=on-failure
       NotifyAccess=main
       RestartUSec=100ms
       TimeoutStartUSec=1min 30s
       TimeoutStopUSec=1min 30s
       TimeoutAbortUSec=1min 30s
       TimeoutStartFailureMode=terminate
       TimeoutStopFailureMode=terminate
       RuntimeMaxUSec=infinity
       WatchdogUSec=infinity
       WatchdogTimestampMonotonic=0
       RootDirectoryStartOnly=no
       RemainAfterExit=no
       GuessMainPID=yes
       RestartPreventExitStatus=255
       MainPID=0
       ControlPID=0
       FileDescriptorStoreMax=0
       NFileDescriptorStore=0
       StatusErrno=0
       Result=success
       ReloadResult=success
       CleanResult=success
       UID=[not set]
       GID=[not set]
       NRestarts=0
       OOMPolicy=stop
       ExecMainStartTimestampMonotonic=0
       ExecMainExitTimestampMonotonic=0
       ExecMainPID=0
       ExecMainCode=0
       ExecMainStatus=0


Также можно вывести конкретный параметр с помощью флага ``-p``. Например чтобы получить *Result* можно использовать команду:

.. code-block:: bash

       root@server# systemctl show ssh -p Result
       Result=success
      
      
Редактирование Unit-файлов.
---------------------------
В *systemctl* есть команда ``edit`` для редактирования Unit-файлов. 

.. code-block:: bash

       root@server# systemctl edit имя_службы


В этом случае будет создан каталог с добавленным к имени службы расширением `.d`. В нём будут храниться любые изменения.
Для использования основного Unit-файла нужно добавить параметр `--full`

.. code-block:: bash

       root@server# systemctl edit имя_службы --full


Удаление Unit-файлов
--------------------
Удалить Unit-файл можно с помощью следующих команд

.. code-block:: bash

       root@server# rm -r /etc/systemd/system/имя_службы.service.d
       root@server# rm /etc/systemd/system/имя_службы.service


После этого нужно выполнить команду для применения изменений

.. code-block:: bash

       root@server# systemctl daemon-reload


Использование целей
===================

Что такое системная цель?
-------------------------
Существуют специальные Unit-файлы, которые описывают определённое состояние системы или точку синхронизации. У этих файлов есть специальное расшерение `.target`.
Эти файлы используются для перевода системы в определённое состояние.

Список целей
------------
Для перечисления всех системных целей можно использовать следующую команду

.. code-block:: bash

       root@server# systemctl list-unit-files --type=target


Для перечисления активных целей нужно использовать другую команду

.. code-block:: bash

       root@server# systemctl list-units --type=target


Остановка или перезапуск машины
===============================
*systemctl* можно использовать для остановки, выключения или перезагрузки машины.
Остановка:

.. code-block:: bash

       root@server# systemctl halt


Полное выключение:

.. code-block:: bash

       root@server# systemctl poweroff


Перезагрузка

.. code-block:: bash

       root@server# systemctl reboot


Вывод
=====
Утилита *systemctl* — это гибкий , универсальный и простой в использовании инструмент, с помощью которого мы можем контролировать и взаимодействовать с системой *systemd* для создания, изменения или удаления Unit-файлов.


Практическая часть
==================
Задача 1. Запуск/ остановка служб
---------------------------------
Посмотрите список всех запущенных служб с помощью команды `systemctl`. Остановите одну запущенную службу, а затем верните её в исходное состояние (подсказка: используйте утилиту `systemctl` + команду)

Задача 2. Отображение файла модуля
----------------------------------
Команда `cat` была добавлена в версию `systemd` 209 и отображает файл модуля. Выведете на экран файл модуля демона-планировщика `atd`.

Задача 3. Редактирование файла модуля
-------------------------------------
Выведете список всех активных модулей, о которых знает `systemd`, затем отредактируйте файл модуля и удалите весь отредактированный файл (после удаления файла или каталога необходимо перезагрузить процесс `systemd`, чтобы он больше не пытался ссылаться на эти файлы и не возвращался к использов18_анию системных копий - для этого можно ввести следующую команду: systemctl daemon-reload)
