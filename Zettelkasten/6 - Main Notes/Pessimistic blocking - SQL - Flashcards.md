
Theory for the cards: [[Pessimistic blocking - SQL]]

FILE TAGS: sql java_interview

Q: На како промежуток времени, по какой логике, для кого блокируется чтение записи при запросе типа `SELECT ... FOR SHARE`?
A: При запросе типа `SELECT ... FOR SHARE` происходит pessimistic blocking записи в БД. Данная блокировка разрешает одновременное чтение записей с `SELECT ... FOR SHARE`, но транзакции типа `SELECT ... FOR UPDATE` ставит в очередь, пока все текущие транзакции чтения записи не завершатся (`COMMIT`/`ROLLBACK`).
<!--ID: 1769251773199-->
