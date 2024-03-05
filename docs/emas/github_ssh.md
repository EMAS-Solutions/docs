# Github EMAS SSH

En muchos repositorios, para poder colaborar con el código, es necesario tener una llave SSH enlazada a tu cuenta de GitHub. Esto es necesario para poder hacer _push_ y _pull_ de los cambios en el repositorio.

## Generar una llave SSH

Para generar una llave SSH, primero debemos abrir una terminal y ejecutar el siguiente comando:

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

Nos pedirá una ubicación para guardar la llave, por defecto es `~/.ssh/id_ed25519`. Si no queremos cambiar la ubicación, simplemente presionamos _Enter_.

Luego nos pedirá una contraseña, __no recomendamos poner contraseña__ ya que obstaculiza el funcionamiento de GitHub, simplemente presionamos _Enter_ dos veces.

## Agregar la llave SSH a ssh-agent

El `ssh-agent` es un programa que guarda las llaves SSH en memoria y las utiliza para autenticar las conexiones SSH. Si no lo tenemos corriendo, debemos iniciar el servicio con el siguiente

```bash
eval "$(ssh-agent -s)"
```

Luego, agregamos la llave SSH al `ssh-agent` con el siguiente comando:

```bash
ssh-add ~/.ssh/id_ed25519
```

## Agregar la llave SSH a GitHub

Para agregar la llave SSH a GitHub, primero debemos copiar el contenido de la llave __publica__ con el siguiente comando:

```bash
cat ~/.ssh/id_ed25519.pub
```

Luego, debemos ir a la configuración de nuestra cuenta de GitHub, en la sección de __SSH and GPG keys__ y hacer click en __New SSH key__. Pegamos el contenido de la llave en el campo __Key__ y le damos un nombre descriptivo.