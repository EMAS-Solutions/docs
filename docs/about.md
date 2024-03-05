# Que es esto?
## Libreta virtual de EMAS
Éste sitio web contiene gran parte de la documentación de EMAS.  
  
### Donde se encuentra esto?
Las páginas disponibles aquí estan en un [repositorio de GitHub](https://github.com/emas-solutions/emas-docs) servido al público a través del servicio
de [GitHub Pages](https://pages.github.com/), con la gestión de dominio de __Cloudflare__.

El dominio de este sitio _([docs.emas-solutions.com](https://docs.emas-solutions.com))_, es gestionado por Cloudflare a través de un registro __A__ que apunta a la dirección IP de GitHub Pages.

### Puedo editar esta documentación?
Es necessaria una mínima experiencia con __Markdown__ y __Git__ para poder editar la documentación.

1. Clona el repositorio en tu máquina local con `git clone <URL>`
2. Empieza el proceso de desarrollo con `mkdocs serve`[*](#mkdocs)
3. Edita los archivos con tu editor de texto favorito en formato __Markdown__
4. Sube los cambios con `git push`
5. Para aplicar los cambios en el sitio web, se debe confirmar de que todo es correcto y luego ejecutar `mkdocs gh-deploy`.

#### Mkdocs
Mkdocs es la herramienta que se utiliza para montar esta pagina. Esta contruido con __Python__ y se puede instalar con `pip install mkdocs` una vez ya disponemos de python en el equipo.

<style>
.search-results {
  display: none;
}
</style>
