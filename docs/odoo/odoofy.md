# Guía básica de Odoofy

La herramienta Odoofy permite crear un entorno de desarrollo para Odoo en una máquina UNIX (Linux o MACOS, de momento no soporta Windows aunque puede instalarse en WSL). El ejecutable de Odoofy se puede descargar desde el siguiente enlace: [Descargar Odoofy](https://github.com/GenisEMAS/Odoofy/releases/download/Release/odoofy).

## Funcionamiento de Odoofy

Odoofy consta de dos partes: la función de **servidor** web para ejecutar comandos desde la página https://odoofy.emas-solutions.es y la función **build** que permite crear un entorno de desarrollo de Odoo. La pagina web de odoofy montara las instancias Odoo en la maquina en la que este preparada. 

Una vez se monte la instancia, se podra acceder a ella desde la ip de la máquina y el puerto que se haya configurado. En estos momentos *(11-01-2024)* la web de odoofy esta en *(192.168.1.207)* desde la red de EMAS. 

Utilizaremos la función build para el montaje rápido de entornos, ya que montar un servidor web requiere de una configuración más compleja.

### Función build

Una vez que se ha descargado el programa Odoofy en una máquina, se puede ejecutar directamente:

`./odoofy -h` Esto mostrará información sobre la herramienta (esto asume que **odoofy** se encuentra en la misma carpeta en la que estamos).

Si ejecutamos `./odoofy build` directamente, montará un entorno de desarrollo de Odoo en la máquina en la que estamos, con la versión de Odoo **16** y el nombre/puerto **16000** *(11/01/2024)*.

> Luego podemos acceder con `localhost:16000` o `ip:16000` si estamos en remoto, desde el navegador.

Para cambiar la versión o configuración de Odoo a montar, se puede utilizar un archivo como parámetro. Por ejemplo:

`./odoofy build configuracion.json`.

Un ejemplo de la estrucura del archivo de configuración es el siguiente:

```json
{
  "version": "17",
  "nombre_proyecto": "01",
  "nombre_servidor": "localhost",
  "idiomas": [
    "es_ES",
    "en_US"
  ],
  "enterprise": true,
  "developer_mode": true,
  "modules": [
    "mail"
  ],
  "preprod": false,
}
```

Todos los parámetros son obligatorios, el orden no importa, pero el nombre de los parámetros sí, ya que son los que utiliza Odoofy para montar el entorno.

### Cosas a tener en cuenta
- La versión y el nombre del proyecto siempre deben tener 2 dígitos, por ejemplo: **16** y **02**, ya que Odoofy utiliza estos valores para elegir el puerto a utilizar.
- `enterprise: true` solo modificará el puerto y nombre de la base de datos, por ejemplo, de **16002** a **16102**. Actualmente, si en la máquina no existe ningún proyecto enterprise, este no será de tipo enterprise.
- El modo desarrollador llena la base de datos con datos de prueba.
- `preprod: true` no afecta a Odoo directamente, sino que elige puertos alternativos como **16202** para normal y **16302** para enterprise.
- Finalmente, los módulos son los que se instalarán en el entorno. En la lista de `modules`, estos deben tener el nombre exacto que se muestra en su información. La función no ha sido completamente probada con módulos de terceros y se recomienda esperar a instalarlos manualmente (mail viene por defecto).

### Notas

Estructura de carpetas de Odoofy:

```
/opt/odoo16/
├── addons
├── framework
├── projects
│   ├── 01
│   ├── enterprise.conf
│   ├── enterprise.log
│   ├── enterprise.service
│   ├── odoo.conf
│   ├── odoo.log
│   ├── odoo.service
│   ├── addons
│   │   ├── community
│   │   ├── custom
│   │   ├── private
│   │   └── public
│   ├── resources
│   │   ├── geoip
│   │   ├── screencasts
│   │   └── screenshots
│   └── runtime
│       ├── enterprise
│       └── odoo
└── venv
```

Desglose de los puertos utilizados por Odoofy:

**16022**
```
16 = Versión de Odoo  
0  = No es enterprise ni preproducción
22 = Número de proyecto
```

Tabla de puertos utilizados por Odoofy:

| Versión | Enterprise | Preproducción | Puertos (web - data) |
|---------|------------|---------------|--------|
| 16      | No         | No            | 16000 - 16500  |
| 16      | Sí         | No            | 16100 - 16600  |
| 16      | No         | Sí            | 16200 - 16700  |
| 16      | Sí         | Sí            | 16302 - 16800  |

<style>
.search-results {
  display: none;
}
</style>

