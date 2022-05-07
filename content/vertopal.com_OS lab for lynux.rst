.. container:: cell markdown

   Модуль os

.. container:: cell markdown

   Модуль os предоставляет множество функций для работы с операционной
   системой, причём их поведение, как правило, не зависит от ОС, поэтому
   программы остаются переносимыми. Здесь будут приведены наиболее часто
   используемые из них.

   Будьте внимательны: некоторые функции из этого модуля поддерживаются
   не всеми ОС.

   os.name - имя операционной системы. Доступные варианты: 'posix',
   'nt', 'mac', 'os2', 'ce', 'java'.

   os.environ - словарь переменных окружения. Изменяемый (можно
   добавлять и удалять переменные окружения).

   os.getlogin() - имя пользователя, вошедшего в терминал (Unix).

   os.getpid() - текущий id процесса.

   os.uname() - информация об ОС. возвращает объект с атрибутами:
   sysname - имя операционной системы, nodename - имя машины в сети
   (определяется реализацией), release - релиз, version - версия,
   machine - идентификатор машины.

   os.access(path, mode, \*, dir_fd=None, effective_ids=False,
   follow_symlinks=True) - проверка доступа к объекту у текущего
   пользователя. Флаги: os.F_OK - объект существует, os.R_OK - доступен
   на чтение, os.W_OK - доступен на запись, os.X_OK - доступен на
   исполнение.

   os.chdir(path) - смена текущей директории.

   os.chmod(path, mode, \*, dir_fd=None, follow_symlinks=True) - смена
   прав доступа к объекту (mode - восьмеричное число).

   os.chown(path, uid, gid, \*, dir_fd=None, follow_symlinks=True) -
   меняет id владельца и группы (Unix).

   os.getcwd() - текущая рабочая директория.

   os.link(src, dst, \*, src_dir_fd=None, dst_dir_fd=None,
   follow_symlinks=True) - создаёт жёсткую ссылку.

   os.listdir(path=".") - список файлов и директорий в папке.

   os.mkdir(path, mode=0o777, \*, dir_fd=None) - создаёт директорию.
   OSError, если директория существует.

   os.makedirs(path, mode=0o777, exist_ok=False) - создаёт директорию,
   создавая при этом промежуточные директории.

   os.remove(path, \*, dir_fd=None) - удаляет путь к файлу.

   os.rename(src, dst, \*, src_dir_fd=None, dst_dir_fd=None) -
   переименовывает файл или директорию из src в dst.

   os.renames(old, new) - переименовывает old в new, создавая
   промежуточные директории.

   os.replace(src, dst, \*, src_dir_fd=None, dst_dir_fd=None) -
   переименовывает из src в dst с принудительной заменой.

   os.rmdir(path, \*, dir_fd=None) - удаляет пустую директорию.

   os.removedirs(path) - удаляет директорию, затем пытается удалить
   родительские директории, и удаляет их рекурсивно, пока они пусты.

   os.symlink(source, link_name, target_is_directory=False, \*,
   dir_fd=None) - создаёт символическую ссылку на объект.

   os.sync() - записывает все данные на диск (Unix).

   os.truncate(path, length) - обрезает файл до длины length.

   os.utime(path, times=None, \*, ns=None, dir_fd=None,
   follow_symlinks=True) - модификация времени последнего доступа и
   изменения файла. Либо times - кортеж (время доступа в секундах, время
   изменения в секундах), либо ns - кортеж (время доступа в
   наносекундах, время изменения в наносекундах).

   os.walk(top, topdown=True, onerror=None, followlinks=False) -
   генерация имён файлов в дереве каталогов, сверху вниз (если topdown
   равен True), либо снизу вверх (если False). Для каждого каталога
   функция walk возвращает кортеж (путь к каталогу, список каталогов,
   список файлов).

   os.system(command) - исполняет системную команду, возвращает код её
   завершения (в случае успеха 0).

   os.urandom(n) - n случайных байт. Возможно использование этой функции
   в криптографических целях.

   os.path - модуль, реализующий некоторые полезные функции на работы с
   путями.

.. container:: cell markdown

   Задача 1. Пользуясь расширением os узнайте список папок рабочего
   стола.

.. container:: cell code

   .. code:: python

      #решение
      import os
      list=[]
      for f in os.listdir(path="C:\\Users\\baska\\Desktop"):
          name, ext = os.path.splitext(f)
          if ext == '':
              list.append(name)
      print (list)

   .. container:: output stream stdout

      ::

         ['chr', 'HW4', 'rfhnjgkz gj h;fnm', 'sql', 'Tor Browser', '[[1-4 сем]] Редкозубов - конспекты лекций (2015)', 'вита', 'ккм попытка 2', 'лабы', 'пикчес', 'Эмулятор PS2']

.. container:: cell raw

   .. raw:: ipynb

      Задача 2.
      выведите древовидную структуру этих папок

.. container:: cell code

   .. code:: python

      #решение

      def list_files(startpath):  
         for root, dirs, files in os.walk(startpath):
             if dir!= '.git':
                 level = root.replace(startpath, '').count(os.sep)
                 indent = ' ' * 4 * (level)
                 print('{}{}/'.format(indent, os.path.basename(root)))
                 subindent = ' ' * 4 * (level + 1)
                 for f in files:
                     print('{}{}'.format(subindent, f))
      list_files("Desktop")

.. container:: cell markdown

   Задача 3. создайте на рабочем столе текстовый документ и внесите в
   него текст из предыдущего задания

.. container:: cell code

   .. code:: python

      os.chdir("C:\\Users\\baska\\Desktop")
      text_file = open("text.txt", "a")
      text_file.write('greetings')
      def list_files_input(startpath,file):  
         for root, dirs, files in os.walk(startpath):
             if dir!= '.git':
                 level = root.replace(startpath, '').count(os.sep)
                 indent = ' ' * 4 * (level)
                 file.write('{}{}/'.format(indent, os.path.basename(root)))
                 subindent = ' ' * 4 * (level + 1)
                 for f in files:
                     file.write('{}{}'.format(subindent, f))
      list_files_input("Desktop",text_file)

.. container:: cell markdown

   Задача 4. переименуйте файл

.. container:: cell markdown

   Задача 5. узнайте размер файла

.. container:: cell markdown

   Задача 6. удалите файл

.. container:: cell code

   .. code:: python
