# Microsoft SQL Server Developer 2019 (including CU8) for Windows Containers

**Intended Use**: Development and Testing only. Not supported in production environments.

If you are looking for the official Microsoft image for SQL Server Developer, go [here](https://hub.docker.com/r/microsoft/mssql-server-windows-developer)

## Requirements

This image is compatible with Windows Server 2016 (Core, with Desktop) and Windows 10 (Professional, Enterprise - Anniversary Edition) OS Hosts. Visit [this](https://msdn.microsoft.com/en-us/virtualization/windowscontainers/deployment/system_requirements) link for the full list of Windows container requirements.

### Getting Started Links

- [Windows Containers on Windows Server](https://msdn.microsoft.com/en-us/virtualization/windowscontainers/quick_start/quick_start_windows_server)
- [Windows Containers on Windows 10](https://msdn.microsoft.com/en-us/virtualization/windowscontainers/quick_start/quick_start_windows_10)

## How to use this image

### Start a basic mssql server instance

```json
docker run --name SQLServer -d -p 1433:1433 -e sa_password=<SA_PASSWORD> nielsvdc/mssql-server-windows-developer
```

#### Connect to Microsoft SQL Server

You can connect to the SQL Server instance from inside the same container by using the [sqlcmd utility](https://msdn.microsoft.com/en-us/library/ms162773.aspx), or from outside the container by downloading [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/en-us/library/mt238290.aspx). Follow [this blog](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/13/sql-server-2016-express-edition-in-windows-containers/) for detailed steps on how to connect to the SQL Server instance from inside or outside the container.

#### Environment Variables

- **sa_password**: The system administrator (userid = 'sa') password used to connect to SQL Server once the container is running. The password must meet the password complexity requirements found [here](https://docs.microsoft.com/en-us/sql/relational-databases/security/password-policy?view=sql-server-2017).

- **ACCEPT_EULA** (optional parameter): Confirms acceptance of the end user licensing agreement found [here](http://go.microsoft.com/fwlink/?LinkId=746388). By default this is set to Yes.

- **attach_dbs** (optional parameter): The configuration for attaching custom DBs (.mdf, .ldf files). The configuration is in single line JSON format. The following example shows all parameters in action and look further in this overview for an exmaple:

```json
[
  {
    "dbName": "AdventureWorks2019",
    "dbFiles": [
      "C:\\SqlServer\\Data\\AdventureWorks2019.mdf",
      "C:\\SqlServer\\Log\\AdventureWorks2019_log.ldf"
    ]
  }
]
```

### Start a basic mssql server instance with a secrets file

On your host computer in the default Docker folder (C:\ProgramData\Docker\) create a folder **secrets**. In this folder create a new file named **sa-password** and store the system administrator password for SQL Server in this file. Now there is no need to add the password as an environment variable for the docker run command.

```json
docker run --name SQLServer -d -p 1433:1433 nielsvdc/mssql-server-windows-developer
```

### Start a mssql server instance and redirect data folder for persistent database files

With this example the folder C:\Docker\Volumes\SQLServer on the host computer is mounted to the C:\SqlServer folder in the container image, thus leaving the database files intact when removing the container:

```json
docker run --name SQLServer -d -p 1433:1433 --volume C:\Docker\Volumes\SQLServer:C:\SqlServer -e sa_password=<SA_PASSWORD> nielsvdc/mssql-server-windows-developer
```

### Start a mssql server instance and attach existing database files on the host computer

With this example we are starting the container with a redirected data folder and attaching existing database files. For attaching the existing database files we need to create a JSON formatted text string as below. With this file we attach the files for the AdventureWorks2019 and AdventureWorksDW2019 databases.

```json
[
  {
    "dbName": "AdventureWorks2019",
    "dbFiles": [
      "C:\\SqlServer\\Data\\AdventureWorks2019.mdf",
      "C:\\SqlServer\\Log\\AdventureWorks2019_log.ldf"
    ]
  },
  {
    "dbName": "AdventureWorksDW2019",
    "dbFiles": [
      "C:\\SqlServer\\Data\\AdventureWorksDW2019.mdf",
      "C:\\SqlServer\\Log\\AdventureWorksDW2019_log.ldf"
    ]
  }
]
```

_Note: It's important to use **single quotes** in the text string, or this will not work. Double quotes are eliminated by docker in the environment variables._

To run the container images, use the following command:

```json
docker run --name SQLServer -d -p 1433:1433 --volume D:\Docker\Volumes\SQLServer:c:\SqlServer -e sa_password=<SA_PASSWORD> -e attach_dbs="[{'dbName':'AdventureWorks2019','dbFiles':['C:\\SqlServer\\Data\\AdventureWorks2019.mdf','C:\\SqlServer\\Log\\AdventureWorks2019_log.ldf']},{'dbName':'AdventureWorksDW2019','dbFiles':['C:\\SqlServer\\Data\\AdventureWorksDW2019.mdf','C:\\SqlServer\\Log\\AdventureWorksDW2019_log.ldf']}]" nielsvdc/mssql-server-windows-developer
```
