1. Выполнить pgbench -c8 -P 6 -T 60 -U postgres postgres.
number of transactions actually processed: 31059
number of failed transactions: 0 (0.000%)
latency average = 96.514 ms
latency stddev = 145.522 ms
initial connection time = 95.627 ms
tps = 517.628820 (without initial connection time)

2. Настроить параметры vacuum/autovacuum. Заново протестировать: pgbench -c8 -P 6 -T 60 -U postgres postgres и сравнить результаты.
Примененные настройки autovacuum:
ALTER SYSTEM SET log_autovacuum_min_duration = 0;
ALTER SYSTEM SET autovacuum_max_workers = 10;
ALTER SYSTEM SET autovacuum_naptime = '15s';
ALTER SYSTEM SET autovacuum_vacuum_threshold = 25;
ALTER SYSTEM SET autovacuum_vacuum_scale_factor = 0.05;
ALTER SYSTEM SET autovacuum_vacuum_cost_delay = 10;
ALTER SYSTEM SET autovacuum_vacuum_cost_limit = 1000;

number of transactions actually processed: 30227
number of failed transactions: 0 (0.000%)
latency average = 99.148 ms
latency stddev = 121.300 ms
initial connection time = 107.919 ms
tps = 503.883715 (without initial connection time)

После применения настроек автовакуума увеличилось среднее время задержки между выполнением транзакций, уменьшилось стандартное отклонение задержки, увеличилось время, затраченное на установление первоначального соединения с базой данных, увеличился показатель пропускной способности базы данных. 

3. Создать таблицу с текстовым полем и заполнить случайными или сгенерированными данным в размере 1млн строк. Посмотреть размер файла с таблицей.
135 MB

4. 5 раз обновить все строчки и добавить к каждой строчке любой символ. Посмотреть количество мертвых строчек в таблице и когда последний раз приходил автовакуум.
4999859 - количество мертвых строк, 2025-07-06 20:27:05.383448+03 - последний автовакуум

5. Отключить Автовакуум на таблице и опять 5 раз обновить все строки. Объяснить полученные результаты.
4999601 - количество мертвых строк

Создается по одной мертвой строке на каждую обновленную запись при каждом обновлении. Автовакуум, мертвые строки не удалились.