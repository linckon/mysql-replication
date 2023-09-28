# Setting Up MySQL Replication: Master-Slave Configuration

## Configuring the master

### Creating users and granting privileges

```bash
CREATE USER 'repl'@'%' IDENTIFIED BY 'password';
GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';
FLUSH PRIVILEGES;
FLUSH TABLES WITH READ LOCK;
```

### Getting the master status

```bash
SHOW MASTER STATUS;
```

The result should look like this:

```bash
+---------------+----------+--------------+------------------+-------------------+
| File | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+---------------+----------+--------------+------------------+-------------------+
| binlog.000002 | 1151   |            |               |                 |
+---------------+----------+--------------+------------------+-------------------+
1 row in set (0.00 sec)
```

## Configuring the slave

```bash
CHANGE MASTER TO MASTER_HOST='[master_ip]', MASTER_USER='repl', MASTER_PASSWORD='password', MASTER_LOG_FILE='[log_file_from_master]', MASTER_LOG_POS=[log_position_from_master];
```

In this example, the master IP is `master`, the log file is `binlog.000002` and the log position is `1151`.

```bash
CHANGE MASTER TO MASTER_HOST='master', MASTER_USER='repl', MASTER_PASSWORD='password', MASTER_LOG_FILE='binlog.000002', MASTER_LOG_POS=1151;
START SLAVE;
```

### Testing the replication

Create a new table on the master:

```bash
CREATE TABLE test (id INT NOT NULL PRIMARY KEY AUTO_INCREMENT, name VARCHAR(255));
```

Insert some data:

```bash
INSERT INTO test (name) VALUES ('test');
INSERT INTO test (name) VALUES ('test2');
```

Now, check and see if the data is replicated on the slave:

```bash
SELECT * FROM test;
```

If you do not see the data, check the slave status:

```bash
SHOW SLAVE STATUS;
```
