## Fastest way to import big data into MYSQL

Importing csv files from a local mysql server is much faster than external environment.

### Check secure file path
```
SELECT @@GLOBAL.secure_file_priv;
```

### Place data csv file on secure file path

#### TIP, You need vagrant plugin for vagrant scp
```
vagrant plugin install vagrant-scp
vagrant scp ${FILE_PATH} ${VM_NAME}:${VM_FILE_PATH}
```

### Load data csv
```
LOAD DATA INFILE "${LOCAL_FILE_PATH}" INTO TABLE ${TABLE_NAME} FIELDS TERMINATED BY ',' ENCLOSED BY '"' LINES TERMINATED BY '\n' IGNORE 1 ROWS;

ex) LOAD DATA INFILE "/var/lib/mysql-files/init-data.csv" INTO TABLE product FIELDS TERMINATED BY ',' ENCLOSED BY '"' LINES TERMINATED BY '\n' IGNORE 1 ROWS;
```
