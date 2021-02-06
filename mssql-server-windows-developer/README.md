# mssql-server-windows-developer-2019

Microsoft SQL Server Developer 2019 for Windows Containers with cumulative update 8.

## Run docker container

After pulling the image to docker, run the container with the following commandline:\
`docker run --name SQLServer -d -p 1433:1433 -e sa_password=Password_01 -e nielsvdc/mssql-server-windows-developer`

You can now connect to the SQL Server using SQL Server Management Studio connecting to localhost, along with sa as username and your defined password.

## Run docker container with persistent database files

What if you do not want to store a database in the container and be able to reuse it even after a container is created?

- In the docker volumes folder, create a SQLServer folder
- Run docker with the following commandline:\
  `docker run --name SQLServer -d -p 1433:1433 --volume C:\ProgramData\Docker\Volumes\SQLServer:c:\SqlServer -e sa_password=Password_01 -e nielsvdc/mssql-server-windows-developer`

In the container the folder C:\SQLServer is created and it is redirected to C:\ProgramData\Docker\Volumes\SQLServer on the host computer. In this folder on the host computer you can store your database and log files or your backup files to restore your database from.
