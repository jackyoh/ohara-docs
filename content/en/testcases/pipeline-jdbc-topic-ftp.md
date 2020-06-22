---
title: JDBC to Topic to FTP
linktitle: JDBC to Topic to FTP
toc: true
type: docs
date: "2020-06-15T00:00:00+01:00"
draft: false
menu:
  testcases:
    parent: Test cases
    weight: 30
---

- [Create a new workspace with three nodes](#create-a-new-workspace-with-three-nodes)
- [Create new topic into the workspace](#create-a-new-topic-in-the-workspace)
- [Create a new pipeline, add a jdbc source connector, topics and a ftp sink connector](#create-a-new-pipeline-add-a-jdbc-source-connector-topics-and-a-ftp-sink-connector)
- [連接 JDBC source -> Topic -> FTP sink](#連接-jdbc-source---topic---ftp-sink)
- [Prepare the required table and data on the PostgreSQL server](#prepare-the-required-table-and-data-on-the-postgresql-server)
- [Prepare the required output folder on the FTP server](#prepare-the-required-output-folder-on-the-ftp-server)
- [Start all connectors on the secondpipeline page](#start-all-connectors-on-the-secondpipeline-page)
- [Verify which test data was successfully dumped to FTP server](#verify-which-test-data-was-successfully-dumped-to-ftp-server)

## Create a new workspace with three nodes

**Add three nodes:**

1. On the Nodes page, click the <kbd>NEW NODE</kbd> button.
2. Enter `<ohara_node_host>` in the **Node** field.
3. Enter `<ohara_node_port>` in the **Port** field.
4. Enter `<node_user>` in the **User** field.
5. Enter `<node_password>` in the **Password** field.
6. Click <kbd>TEST CONNECTION</kbd> to test your connection.
7. If the test passes, click <kbd>ADD</kbd>.
8. Repeat the above steps to add **three nodes**.

**Create a new workspace:**

1. On the Workspaces page, click the <kbd>NEW WORKSPACE</kbd> button.
2. Enter “wk01” in the **Name** field.
3. Select all available nodes from the Node List.
4. Add plugins:
   1. Click the <kbd>NEW PLUGIN</kbd> button.
   2. Select `postgresql-9.1-901-1.jdbc4.jar` form your disk (download the jar [here](https://repo1.maven.org/maven2/postgresql/postgresql/9.1-901-1.jdbc4/postgresql-9.1-901-1.jdbc4.jar)), the jar should be displayed in the **Plugin List**, make sure you check the jar you just uploaded.
5. Click <kbd>ADD</kbd>.

## Create a new topic in the workspace

**Add a new topic:**

1. On the **Workspaces** > **wk01** > **TOPICS** tab, click the <kbd>NEW TOPIC</kbd> button.
2. Enter “t3” in the **Topic name** field and enter default value in other fields, click <kbd>ADD</kbd>.

## Create a new pipeline, add a jdbc source connector, topics and a ftp sink connector

1. On the **Pipelines** list page, click the <kbd>NEW PIPLINE</kbd> button.
2. Enter “secondpipeline” in the **Pipeline name** field and select “wk01” from the Workspace name dropdown. Then, click <kbd>ADD</kbd>.
3. Click the **Add a source connector** icon and select **oharastream.ohara.connector.jdbc.source.JDBCSourceConnector** from the list, then click <kbd>ADD</kbd>.
4. Enter “jdbcsource” in the **myconnector** field and click <kbd>ADD</kbd>.
5. Click the **Add a topic** icon and select **t3** from the dropdown and click <kbd>ADD</kbd>.
6. Click the **Add a sink connector** icon and select **oharastream.ohara.connector.ftp.FtpSink** from the list, then click <kbd>ADD</kbd>.
7. Enter “ftpsink” in the **myconnector** field and click <kbd>ADD</kbd> button.

## 連接 JDBC source -> Topic -> FTP sink

**Set up jdbcsource connector:**

1. On the **secondpipeline** page, click the **jdbcsource** graph in the pipeline graph.
2. Select the **COMMON** tab, fill out the form with the following config:
   1. Enter “<jdbc_url>” (jdbc url for PostgreSQL server) in the **jdbc url** field.
   2. Enter “<database_username>“ in the **user name** field.
   3. Enter “<database_password>“ in the **password** field.
   4. Enter “<database_table>” in the **table name** field.
   5. Enter “<table_timestamp>” in the **timestamp column name** field.
   6. Change flush size from 1000 to 10 in the **JDBC flush Size** field.
3. Select the **CORE** tab, select the **t3** from the **Topics** dropdown.

**Set up ftpsink connector:**

1. Click the **ftpsink** object in pipeline graph.
2. Select the **COMMON** tab, enter the following in the fields.
   1. Enter “/demo/output” in the **Output Folder** field.
   2. Click the **write header** checkbox, make it checked.
   3. Enter `<ftp_server_ip>` in the **Hostname of FTP Server** field.
   4. Enter `<ftp_server_port>` in the **Port of FTP Server** field.
   5. Enter `ftp_username` in the **User of FTP Server** field.
   6. Enter `ftp_password` in the **Password of FTP Server** field.
3. Select the **CORE** tab and select **t3** from the **Topics** dropdown.

## Prepare the required output folder on the FTP server

1. Open a terminal, login to FTP server (or use a FTP client of your choice)

```
$ ftp `ftp_server_ip`
Name: `ftp_username`
Password: `ftp_password`
```

2. Create the following folders.

```
ftp> mkdir demo
ftp> cd demo
ftp> mkdir output
ftp> bye
```

## Prepare the required table and data on the PostgreSQL server

**Check database has table and data:**

1. Open a terminal and log into the PostgreSQL server.

```
$ psql -h <PostgreSQL_server_ip> -W <database_name> -U <user_name>
```

2. check table is exist

```
postgres=# \dt
          List of relations
 Schema |    Name     | Type  | Owner
--------+-------------+-------+-------
 public | person_data | table | ohara
 public | test_data   | table | ohara
(2 rows)
```

3. check table info

```
postgres=# \d person_data
                        Table "public.person_data"
  Column   |            Type             | Collation | Nullable | Default
-----------+-----------------------------+-----------+----------+---------
 index     | integer                     |           | not null |
 name      | character varying           |           |          |
 age       | integer                     |           |          |
 id        | character varying           |           |          |
 timestamp | timestamp without time zone |           |          | now()

```

4. check table has data

```
postgres=# select * from person_data;
 index |  name   | age |     id     |      timestamp
-------+---------+-----+------------+---------------------
     1 | Sam     |  33 | H123378803 | 2019-03-08 18:52:00
     2 | Jay     |  25 | A159330943 | 2019-03-08 18:53:00
     3 | Leon    |  31 | J156498160 | 2019-03-08 19:52:00
     4 | Stanley |  40 | D113134484 | 2019-03-08 20:00:00
     5 | Jordan  |  21 | U141236791 | 2019-03-08 20:10:20
     6 | Kayden  |  20 | E290773637 | 2019-03-09 18:52:59
     7 | Dillon  |  28 | M225842758 | 2019-03-09 20:52:59
     8 | Ross    |  33 | F229128254 | 2019-03-09 20:15:59
     9 | Gunnar  |  50 | Q107872026 | 2019-03-09 21:00:59
    10 | Tyson   |  26 | N197744193 | 2019-03-09 21:05:59
..........
```

[How to create table and insert data?](#how-to-create-table-and-insert-data)

## Start all connectors on the secondpipeline page

1. On the **secondpipeline** page.
2. Click the **Start pipeline** icon.

## Verify which test data was successfully dumped to FTP server

1. Open a terminal, login to FTP server.

```
$ ftp `ftp_server_ip`
Name: `ftp_username`
Password: `ftp_password`
```

2. list all result CSV files in **/demo/output/wk01-t3/partition0** folder.

```
$ ftp ls /demo/output/wk01-t3/partition0
/demo/output/wk01-t3/partition0/wk01-t3-0-000000000.csv
```

3. get the CSV file from ftp server to local.

```
$ ftp cd /demo/output/wk01-t3/wk01
$ ftp get wk01-t3-0-000000000.csv ./wk01-t3-0-000000000.csv
$ ftp bye
```

4. View the content of **wk01-t3-0-000000000.csv**.

```
$ cat wk01-t3-0-000000000.csv
index,name,age,id,timestamp
1,Sam,33,H123378803,2019-03-08 18:52:00.0
2,Jay,25,A159330943,2019-03-08 18:53:00.0
3,Leon,31,J156498160,2019-03-08 19:52:00.0
4,Stanley,40,D113134484,2019-03-08 20:00:00.0
5,Jordan,21,U141236791,2019-03-08 20:10:20.0
6,Kayden,20,E290773637,2019-03-09 18:52:59.0
8,Ross,33,F229128254,2019-03-09 20:15:59.0
7,Dillon,28,M225842758,2019-03-09 20:52:59.0
9,Gunnar,50,Q107872026,2019-03-09 21:00:59.0
10,Tyson,26,N197744193,2019-03-09 21:05:59.0

```

## How to create table and insert data?

1. Create table **person_data**.

```
postgres=# create table person_data (
postgres=#   index INTEGER NOT NULL,
postgres=#   name character varying,
postgres=#   age INTEGER,
postgres=#   id character varying,
postgres=#   timestamp timestamp without time zone DEFAULT NOW()
postgres=# );
```

2. insert data into table **person_data**.

```
postgres=# insert into person_data (index,name,age,id)values(1,'Sam',33,'H123378803'),
	(2,'Jay',25,'A159330943'),
	(3,'Leon',31,'J156498160'),
	(4,'Stanley',40,'D113134484'),
	(5,'Jordan',21,'U141236791'),
	(6,'Kayden',20,'E290773637'),
	(7,'Dillon',28,'M225842758'),
	(8,'Ross',33,'F229128254'),
	(9,'Gunnar',50,'Q107872026'),
	(10,'Tyson',26,'N197744193');
```
