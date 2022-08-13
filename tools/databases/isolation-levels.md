# Transactions' isolation levels

| Isolation Level | Lost Update | Dirty Read  | Non-repeatable Read |   Phantom Read | Serialization Anomaly |
|-----------------|-------------|-------------|---------------------|----------------|-----------------------|
| Read uncommited | Allowed     | Possible    |     Possible        |  Possible      |      Possible         |
| Read commited   | Not Possible| Not Possible|     Possible        |  Possible      |      Possible         |
| Repeatable Read | Not Possible| Not Possible|     Not Possible    |  Allowed       |      Possible         |
|  Serializable   | Not Possible| Not Possible|     Not Possible    |  Not Possible  |      Not Possible     |

By default in Postgres Read Commited  

## Lost Update

Appear when second transaction rewrite the changes of the first transaction before the first transaction end

|               Transaction 1            |            Transaction 2              |
|----------------------------------------|---------------------------------------|
| update t1 set f2 = f2 + 10 where f1=1; |                                       |
|                                        | update t1 set f2 = f2 + 25 where f1=1;|


## Dirty read

Reading of date, added or changed by uncompleted transaction

|               Transaction 1            |            Transaction 2              |
|----------------------------------------------------------------------|-----------------------------------------------------------------------|
| select f2 from t1 where f1 = 1;<br>f2<br>- - - - -<br>100<br>(1 row) |                                                                       |
| update t1 set f2 = 200 where f1=1;                                   |                                                                       |
|                                                                      |  select f2 from t1 where f1 = 1;<br>f2<br>- - - - -<br>200<br>(1 row) |
| ROLLBACK;                                                            |                                                                       |         


## Non-repeatable read

On repeatable read during one transaction, previously read data, are changed

|               Transaction 1            |            Transaction 2          |
|----------------------------------------|-----------------------------------|
|                                        |  select f2 from t1 where f1 = 1;  |
| update t1 set f2 = f2 + 1 where f1=1;  |                                   |
| COMMIT;                                |                                   |
|                                        |  select f2 from t1 where f1 = 1;  |


## Phantom reads

On repeatable read during one transaction, the same selection gives different sets of rows.    
The difference between non-repeatable read is that the result of repeated read changed because of appeared of new (phantom) date, not because of the change/delete of these data itself.  

|               Transaction 1              |            Transaction 2          |
|------------------------------------------|-----------------------------------|
|                                          |  select sum(f2) from t1;          |
| insert into t1 (f1,f2) values (15, 20);  |                                   |
| COMMIT;                                  |                                   |
|                                          |  select sum(f2) from t1;          |

How to resolve: remember the last added row at the moment of beginning of transaction


## Anomaly Serialization

Situation, when parallel execution of transactions leads to the result, that is impossible in consecutive (sequence) execution of the same transactions.


|               Transaction 1                                                 |            Transaction 2                                                     |
|-----------------------------------------------------------------------------|------------------------------------------------------------------------------|
| select sum(value) from mytab where class = 1;<br>- - - - -<br>30<br>(1 row) | select sum(value) from mytab where class = 2;<br>- - - - -<br>300<br>(1 row) |
| insert into mytab (value, class) values (30, 2);                            |    insert into mytab (value, class) values (300, 1);                         |
| COMMIT;                                                                     |    COMMIT;                                                                   |





