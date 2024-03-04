## Setup mount para remote backups

Actualmente el [script de backups](#script-de-backup) envia los archivos a la carpeta `/mnt/backups`.
Esta carpeta es un mount del servidor de backups (sincronización de archivos).

Para hacer el montaje seguimos los siguientes pasos:

1. `sudo nano /etc/fstab` Editamos el archivo **fstab**. Inserimos la siguiente línea:

>`//<IP>/BAK/<RUTA> /mnt/backups cifs vers=1.0,iocharset=utf8,file_mode=0777,dir_mode=0777,noperm,credentials=<RUTA CREDENCIALES> 0 0`

- **IP**: Es la dirección IP del servidor de backups. Ej. `192.168.1.5`
- **RUTA**: Es la ruta en el servidor de backups donde se guardaran los archivos. Ej. `emas/88-Proyectos/Backups`  
- **RUTA CREDENCIALES**: Es la ruta del archivo de credenciales. Ej. `/home/emas/.backup-credentials`


2. Creamos el archivo `.backup-credentials` en la raíz del usuario. I ponemos los credenciales del servidor de backup:  
`domain=EMASIT`
`username=Developer`  
`password=D3v3#2021`

3. Aseguramos que la utilidad cifs esta instalada `sudo apt install cifs-utils`.
4. Montamos los drives con `sudo mount -a`.

### Gestión errores
Dependiendo del sistema operativo del servidor de backups, es posible que tengamos que cambiar el protocolo de montaje.

Si el servidor de backups no es Windows, es posible que tengamos que cambiar `vers=1.0` por `vers=3.0`. _(También más seguro)_.

Para comprobar la connexión a una carpeta compartida podemos utilizar el comando:  
`smbclient -L //<IP>/BAK -U <(DOMINIO)/USUARIO>`.
Aquí podemos comprobar si el servidor es accesible y si los credenciales son correctos. _El dominio es opcional y solo necessario en Windows_.

## Tarea programada
Con el comando: `crontab -e`;
podemos modificar el archivo de tareas automaticas. Añadir la siguiente linia:

```yaml
0 12 * * * <ruta-al-proyecto>/backup_script.sh
```

Cada línia de este archivo añade una tarea que se ejecuta automáticamente en el
tiempo que se le ha asignado.

```
.--------------- minuto (0-59)
|  .------------ hora (0-23)
|  |  .--------- día del mes (1-31)
|  |  |  .------ mes (1-12) o jan,feb,mar,apr,may,jun,jul... (meses en inglés)
|  |  |  |  .--- día de la semana (0-6) (domingo=0 o 7)
|  |  |  |  |
*  *  *  *  *  comando a ejecutar
```

### Script de backup
```bash
#!/bin/bash

# Set variables
backup_dir="/mnt/backups/odoo/17"
date_suffix=$(date +%Y%m%d%H%M%S)

# Ensure the backup directory exists
mkdir -p "$backup_dir"

# Backup the database
curl -X POST -F 'master_pwd=odoo17' -F 'name=odoo17002' -F 'backup_format=zip' -o $backup_dir/02-comatec-$date_suffix.zip http://192.168.1.208:17002/web/database/backup

# Remove old backups (keep only the most recent 5)
ls -dt $backup_dir/* | tail -n +14 | xargs rm -rf
```


En ruta, ponemos la ruta de la carpeta que CONTIENE la base de datos.
En nombre, ponemos el nombre del proyecto (podemos poner subcarpetas para organizar mejor los backups EJ.`/proyecto/nombre_fecha`).

<style>
.search-results {
  display: none;
}
</style>
