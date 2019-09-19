# MariaDB Tools

This tool is written in Python, scans all the Binary logs and generates a report containing all the DML/DDL operations.

**Note:** _Don't try to execute this on live binlog, best to copy the files in some other server or a slave and process._

### List of arguments
- Binary Log Path
  - This parameter locates the path of the MariaDB binary logs. Must not end with a "/"
- Binary Log Base Filename
  - This is the base filename of the Binary Log files i.e (mariadb-bin) 
  - This should be the same name that is defined in the `log_bin` parameter in the `server.cnf` file.
    - https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin
- Binary Log Starting Number
  - This is the starting binary log number, refer to the examples below for better understanding. 

### Examples

Parsiong the the binary logs starting with 00001 to the last one. Take note of the last parameter is the starting binlog number.

```
root@2c44850ee333:~# python binlogreader.py /var/log/mysql mariadb-bin 1

*********** Reading Binary Logs ***********
Reading /var/log/mysql/mariadb-bin.000001...
Reading /var/log/mysql/mariadb-bin.000002...
Reading /var/log/mysql/mariadb-bin.000003...
Reading /var/log/mysql/mariadb-bin.000004...
Reading /var/log/mysql/mariadb-bin.000005...
Reading /var/log/mysql/mariadb-bin.000006...
Reading /var/log/mysql/mariadb-bin.000007...
Reading /var/log/mysql/mariadb-bin.000008...
*********** Finished Reading Binary Logs ***********

*********** Getting Distinct Changes ***********
*********** Distinct Changes Identified ***********

*********** Generating Report ***********
Generating Summary for [### DELETE FROM `testdb`.`tab`]
Generating Summary for [### INSERT INTO `testdb`.`junk`]
Generating Summary for [### INSERT INTO `testdb`.`tab`]
Generating Summary for [### INSERT INTO `testdb`.`testtab`]
Generating Summary for [### UPDATE `testdb`.`junk`]
Generating Summary for [### UPDATE `testdb`.`tab`]
Generating Summary for [CREATE TABLE tab(id serial, col varchar(100))]
Generating Summary for [DROP TABLE `junk` /* generated by server */]
*********** Report Generated ***********

*********** Start of the Binary Log Report ***************
DELETE FROM `testdb`.`tab`,217
INSERT INTO `testdb`.`junk`,100
INSERT INTO `testdb`.`tab`,5244
INSERT INTO `testdb`.`testtab`,5147648
UPDATE `testdb`.`junk`,2
UPDATE `testdb`.`tab`,322
CREATE TABLE tab(id serial, col varchar(100)),1
DROP TABLE `junk` /* generated by server */,1
*********** End of the Binary Log Report ***************
```

The output above indicates that all the binary logs were processed starting from 000001 to 000005 and the binary log repor covers all the transactions from the 5 logs mentioned.

Asuming we want to process on the 000004 and later files, we can trigger the script as follows. The report will be generated accordingly.

```
root@2c44850ee333:~# python binlogreader.py /var/log/mysql mariadb-bin 4

*********** Reading Binary Logs ***********
Skipping binlog file 1
Skipping binlog file 2
Skipping binlog file 3
Reading /var/log/mysql/mariadb-bin.000004...
Reading /var/log/mysql/mariadb-bin.000005...
Reading /var/log/mysql/mariadb-bin.000006...
Reading /var/log/mysql/mariadb-bin.000007...
Reading /var/log/mysql/mariadb-bin.000008...
*********** Finished Reading Binary Logs ***********

*********** Getting Distinct Changes ***********
*********** Distinct Changes Identified ***********

*********** Generating Report ***********
Generating Summary for [### INSERT INTO `testdb`.`junk`]
Generating Summary for [### INSERT INTO `testdb`.`tab`]
Generating Summary for [### INSERT INTO `testdb`.`testtab`]
Generating Summary for [### UPDATE `testdb`.`junk`]
Generating Summary for [DROP TABLE `junk` /* generated by server */]
*********** Report Generated ***********

*********** Start of the Binary Log Report ***************
INSERT INTO `testdb`.`junk`,100
INSERT INTO `testdb`.`tab`,100
INSERT INTO `testdb`.`testtab`,5147648
UPDATE `testdb`.`junk`,2
DROP TABLE `junk` /* generated by server */,1
*********** End of the Binary Log Report ***************
```

Next in lime is going to be the option to generate time based report generation on the hourly basis from the given binary logs.

Thank you.
