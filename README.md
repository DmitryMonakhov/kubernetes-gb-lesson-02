## Домашнее задание для к уроку 4 - Хранение данных и ресурсы
### Проверим подключение к созданному экземпляру postgres с помощью отдельного пода:
```sh
kubectl run -t -i --rm --image postgres:10.13 test bash
If you don't see a command prompt, try pressing enter.
```
### Подключимся к базе ```testdb``` от имени пользователя ```testuser```, указанного в манифесте на развертывание сервера postgres:
```sh
root@test:/# psql -h 10.100.219.1 -U testuser testdb
Password for user testuser:
psql (10.13 (Debian 10.13-1.pgdg90+1), server 13.4)
WARNING: psql major version 10, server major version 13.
         Some psql features might not work.
Type "help" for help.

testdb=# \l+
                                                                   List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges   |  Size   | Tablespace |                Description
-----------+----------+----------+------------+------------+-----------------------+---------+------------+--------------------------------------------
 postgres  | testuser | UTF8     | en_US.utf8 | en_US.utf8 |                       | 7901 kB | pg_default | default administrative connection database
 template0 | testuser | UTF8     | en_US.utf8 | en_US.utf8 | =c/testuser          +| 7753 kB | pg_default | unmodifiable empty database
           |          |          |            |            | testuser=CTc/testuser |         |            |
 template1 | testuser | UTF8     | en_US.utf8 | en_US.utf8 | =c/testuser          +| 7753 kB | pg_default | default template for new databases
           |          |          |            |            | testuser=CTc/testuser |         |            |
 testdb    | testuser | UTF8     | en_US.utf8 | en_US.utf8 |                       | 7901 kB | pg_default |
(4 rows)

testdb=#
```
### Создадим в базе ```testdb``` тестовую таблицу ```testable```
```sh

testdb=# CREATE TABLE testtable (testcolumn VARCHAR (50) );
CREATE TABLE
testdb=# \dt
           List of relations
 Schema |   Name    | Type  |  Owner
--------+-----------+-------+----------
 public | testtable | table | testuser
(1 row)

testdb=#
```
### Произведем удаление пода с сервером postgres и убедимся в новом пересозданном поде с сервером postgres в базе ```testdb``` присутствует ранее созданная таблица ```testtable```:
```sh
 ✘ ⚡ ⚙ root@debian-01  ~/project/geekbrains/kubernetes-gb-lesson-02   master ±  k get po
NAME                       READY   STATUS    RESTARTS   AGE
postgres-648555c6f-wpx9k   1/1     Running   0          16m
 ⚡ ⚙ root@debian-01  ~/project/geekbrains/kubernetes-gb-lesson-02   master ±  k delete po postgres-648555c6f-wpx9k
pod "postgres-648555c6f-wpx9k" deleted
Time: 0h:00m:05s
 ⚡ ⚙ root@debian-01  ~/project/geekbrains/kubernetes-gb-lesson-02   master ±  k get po
NAME                       READY   STATUS    RESTARTS   AGE
postgres-648555c6f-79rkt   1/1     Running   0          8s
 ⚡ ⚙ root@debian-01  ~/project/geekbrains/kubernetes-gb-lesson-02   master ± 
```
```sh
kubectl run -t -i --rm --image postgres:10.13 test bash
If you don't see a command prompt, try pressing enter.
root@test:/# psql -h 10.100.219.4 -U testuser testdb
Password for user testuser:
psql (10.13 (Debian 10.13-1.pgdg90+1), server 13.4)
WARNING: psql major version 10, server major version 13.
         Some psql features might not work.
Type "help" for help.

testdb=# \dt
           List of relations
 Schema |   Name    | Type  |  Owner
--------+-----------+-------+----------
 public | testtable | table | testuser
(1 row)

testdb=#
```
#### Как видно из вывода команды оболочки psql ```\dt``` таблица ```testtable``` присутствует в базе ```testdb```
