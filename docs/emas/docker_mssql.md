# SQL Server Docker

Microsoft Sql Server es el proveedor de base de datos de Windows mas popular, por eso muchas aplicaciones empresariales están basadas en esta tecnología. En este documento se describe como instalar y configurar un servidor de base de datos SQL Server en un contenedor Docker.

### Por qué usar Docker para SQL Server
Mientras que SQLServer es una excelente base de datos, su instalación y configuración puede ser complicada debido a su enfoque en máquinas Windows. Docker permite instalar y configurar SQL Server en cualquier sistema operativo que soporte Docker, incluyendo Windows, Linux y MacOS.

### Requisitos
- Una maquina con una instalación de [Docker Engine](https://docs.docker.com/engine/install/) válida.  

## Instalación
Asumiendo que ya tienes Docker instalado, procedemos a la configuración de __docker compose__:
```yml
version: '3.8'
services:
  sql1: # Nombre del contenedor
    image: mcr.microsoft.com/mssql/server:2019-latest # Imagen de SQL Server
    container_name: sql1 # Nombre del contenedor
    hostname: sql1 # Nombre del host
    environment:
      - ACCEPT_EULA=Y # Acepta los términos de uso
      - MSSQL_SA_PASSWORD=D3v3#2021 # Contraseña del usuario SA
    ports:
      - "1433:1433" # Puerto de SQL Server
    volumes:
      - sql1data:/var/opt/mssql # Volumen para persistir los datos
    restart: unless-stopped # Reiniciar el contenedor al reiniciar sistema si no se detiene manualmente

volumes:
  sql1data: # Volumen para persistir los datos
```
> La parte mas importante es la MSSQL_SA_PASSWORD, que es la contraseña del usuario SA. Asegúrate de cambiarla por una contraseña segura y de recordarla, ya que la necesitarás para conectarte a la base de datos.

Guarda el archivo con el nombre `docker-compose.yml` y ejecuta el siguiente comando:
```bash
docker compose up -d
```

Después de unos minutos, el contenedor de SQL Server debería estar corriendo. Puedes verificarlo con el siguiente comando:
```bash
docker ps
```

## Conexión a SQL Server
Para conectarte a SQL Server, puedes usar cualquier cliente de SQL Server, como [Azure Data Studio](https://docs.microsoft.com/en-us/sql/azure-data-studio/download?view=sql-server-ver15) o [SQL Server Management Studio](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15). También puedes usar la línea de comandos con `sqlcmd`:
```bash
docker exec -it sql1 /opt/mssql-tools/bin/sqlcmd -S <IP> -U SA -P 'D3v3#2021'
```
Para conectar necesitamos acceso a la maquina en donde esta el servidor _(si no es el mismo)_ y debemos utilizar su IP como nombre del servidor.
Las credenciales son las que definimos en el archivo `docker-compose.yml`, con usuario __SA__.


<style>
.search-results {
  display: none;
}
</style>