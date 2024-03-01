## Setup mount para remote backups

Actualmente el [script de backups](#script-de-backup) envia los archivos a la carpeta `/mnt/backups`.
Esta carpeta es un mount del servidor de backups (sincronización de archivos).

Para hacer el montaje seguimos los siguientes pasos:

1. `sudo nano /etc/fstab` Editamos el archivo **fstab**. Inserimos el siguiente texto:

>`//<IP>/BAK/<RUTA> /mnt/backups cifs vers=3.0,iocharset=utf8,file_mode=0777,dir_mode=0777,noperm,credentials=<RUTA CREDENCIALES> 0 0`

- **IP**: Es la dirección IP del servidor de backups. Ej. `192.168.60.9`
- **RUTA**: Es la ruta en el servidor de backups donde se guardaran los archivos. Ej. `ODOO_PROYECTO`  
- **RUTA CREDENCIALES**: Es la ruta del archivo de credenciales. Ej. `/home/emas/.backup-credentials`


2. Creamos el archivo `.backup-credentials` en la raíz del usuario. I ponemos los credenciales del servidor de backup:  
`username=odoo`  
`password=B4ckup$2023%`

3. Aseguramos que la utilidad cifs esta instalada `sudo apt install cifs-utils`.
4. Montamos los drives con `sudo mount -a`.

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
ls -dt $backup_dir/* | tail -n +6 | xargs rm -rf
```


En ruta, ponemos la ruta de la carpeta que CONTIENE la base de datos.
En nombre, ponemos el nombre del proyecto (podemos poner subcarpetas para organizar mejor los backups EJ.`/proyecto/nombre_fecha`).

<style>
.search-results {
  display: none;
}
</style>
