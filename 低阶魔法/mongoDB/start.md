# MongoDB start

非关系数据库中的文档数据库 存取的是BSON（Json的进阶版）

### mongodb的自动化服务配置方案

#### Manually Create a Windows Service for MongoDB Community Edition

1

### Open an Administrator command prompt.

Press the `Win` key, type `cmd.exe`, and press `Ctrl + Shift + Enter` to run the **Command Prompt** as Administrator.

Execute the remaining steps from the Administrator command prompt.

2

### Create directories.

Create directories for your database and log files:

copycopied

```
mkdir c:\data\db
mkdir c:\data\log
```

3

### Create a configuration file.

Create a configuration file. The file **must** set [`systemLog.path`](https://docs.mongodb.com/v3.2/reference/configuration-options/#systemLog.path). Include additional [configuration options](https://docs.mongodb.com/v3.2/reference/configuration-options/) as appropriate.

For example, create a file at `C:\Program Files\MongoDB\Server\3.2\mongod.cfg` that specifies both [`systemLog.path`](https://docs.mongodb.com/v3.2/reference/configuration-options/#systemLog.path) and [`storage.dbPath`](https://docs.mongodb.com/v3.2/reference/configuration-options/#storage.dbPath):

copycopied

```powershell
systemLog:
    destination: file
    path: c:\data\log\mongod.log
storage:
    dbPath: c:\data\db
```

4

### Create the MongoDB service.

Create the MongoDB service.

copycopied

```shell
sc.exe create MongoDB binPath= "\"D:\Program Files\MongoDB\Server\3.2\bin\mongod.exe\" --service --config=\"D:\Program Files\MongoDB\Server\3.2\mongod.cfg\"" DisplayName= "MongoDB" start= "auto"
```

`sc.exe` requires a space between “=” and the configuration values (eg “binPath= “), and a “" to escape double quotes.

If successfully created, the following log message will display:

copycopied

```powershell
[SC] CreateService SUCCESS
```

5

### Start the MongoDB service.

copycopied

```powershell
net start MongoDB
```

6

### Stop or remove the MongoDB service as needed.

To stop the MongoDB service, use the following command:

copycopied

```powershell
net stop MongoDB
```

To remove the MongoDB service, first stop the service and then run the following command:

copycopied

```powershell
sc.exe delete MongoDB
```



