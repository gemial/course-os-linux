SSMTP и электронная почта
###########################

:date: 2023-05-04
:summary: SSMTP и электронная почта
:status: published
:author: Киселев Г. А., Дорохович В. А., Семенова Д. К.

.. default-role:: code 
.. contents:: Содержание


Теоретическая часть
====================

Представим следующую ситуацию: на сервере запускается долго работающий скрипт (например, вычисляющий большие простые числа),  и мы хотим получать от сервера уведомления по электронной почте о том, что скрипт работает нормально. Для этих целей можно использовать утилиты SSMTP и Mailutil. Они позволяют атоматизировать отправку электронной почты. Для этого применяется SMTP (Simple Mail Transport Protocol) - протокол передачи данных, используемый для отправки электронной почты.  


Установка SSMTP
----------------
Для установки SSMTP  и Mailutils запустите следующие команды:

.. code-block:: bash

    sudo apt-get install ssmtp
    sudo apt-get install mailutil

После установки необходимо настроить конфигурацию. Для этого запустите команду:

.. code-block:: bash

    sudo nano /etc/ssmtp/ssmtp.conf
    
В текстовом редакторе Nano откроется конфигурвционный файл ssmtp.conf. Для использования почты Gmail туда необходимо вставить следующие строки кода:

.. code-block:: bash

    root=<адрес>@gmail.com
    mailhub=smtp.gmail.com:587
    hostname=smtp.gmail.com:587
    UseSTARTTLS=YES
    AuthUser=<адрес>@gmail.com
    AuthPass=<пароль>
    FromLineOverride=YES
    
Важно! В поле **AuthPass** нужно вводить не пароль от аккаунта Google, а специальный пароль для почтового приложения, который необходимо сгенерировать в настройках Google-аккаунта.
Это можно сделать по следующей ссылке: https://myaccount.google.com/apppasswords. Пароли для приложений можно создать только в том случае, когда для аккаунта включена двухфакторная аутентификация, поэтому предварительно включите её, если она у вас отключена. 

Для сохранения конфигурационного файла нажмите Ctrl+O. Далее необходимо внести изменения в другой конфигурационный файл. Запустите команду:

.. code-block:: bash

    sudo nano /etc/ssmtp/revaliases
    
Туда необходимо вставить следующую строчку:

.. code-block:: bash

    root: <адрес>@gmail.com:smtp.gmail.com:587
    
Сохраняем файл. Установка и настройка конфигурации завершены. 


Использование SSMTP
------------------------

Простейший способ использовать SSMTP - следующая команда:

.. code-block:: bash

    echo "текст_письма" | mail -s "тема_письма" <адрес_получателя>@gmail.com
    
Более интересно применять SSMTP внутри bash-скрипта. Например, создадим следующий скрипт:

.. code-block:: bash

    #!/bin/bash
    for i in `seq 0 9`
    do
            echo $i | mail -s "Hello from server" <адрес-получателя>@phystech.edu
            sleep 2
    done
    
Такой скрипт каждые 2 секунды будет отправлять на указанный электронный адрес письмо с темой "Hello from server", а текст сообщения будет представлять собой число от 0 до 9. Всего будет отправлено 10 писем. 

Рассмотрим более интересный пример: рассылка письма нескольким пользователям. Для начала создадим файл emails.txt, в который поместим список адресатов. 

.. code-block:: bash
    
    <адрес1>@gmail.com
    <адрес2>@yandex.ru
    <адрес3>@phystech.edu
    
Далее создадим bash-скрипт mailing.sh:

.. code-block:: bash

    #!/bin/bash

    file=emails.txt


    while read -r line;
    do
            echo "Hello, $line" | mail -s "Рассылка" $line

    done < "$file"
    
Этот скрипт на каждый из указанных адресов отправит письмо с темой "Рассылка" и текстом "Hello, <адрес>"

Можно усовершенствовать этот пример, добавив возможность рассылки текста из файла нескольким адресатам. Для этого создадим файл letter.txt, в котором напишем текст письма:

.. code-block:: text

    To be, or not to be, that is the question:
    Whether 'tis nobler in the mind to suffer
    The Slings and Arrows of outrageous Fortune
    Or to take arms against a sea of troubles,
    And by opposing, end them. To die, to sleep;
    No more; and by a sleep to say we end
    The heart-ache and the thousand natural shocks
    That flesh is heir to — 'tis a consummation
    Devoutly to be wish'd.


Отредактируем файл mailing.sh:

.. code-block:: bash

    #!/bin/bash

    file=emails.txt


    while read -r line;
    do
            cat letter.txt | mail -s "Hello, $line" $line

    done < "$file"
    
Теперь скрипт будет рассылать по указанным адресам текст письма из файла. 


Практическая часть
==================

Задачи
--------

#. Реализуйте описанный выше пример.

#. Напишите скрипт, который каждые 10 секунд рассылает почтовым адресам из файла информацию о процессах, запущенных в системе. *Указание: список запущенных процессов можно посмотреть командой "ps aux"*

#. Модифицируйте пример из введения так, чтобы он отправлял письмо с текстом из файла не по всем почтовым адресам, а только принадлежащим домену phystech.edu.
