# My Little Netflix

Aplicación web cliente que funciona como una pequeña base de datos de películas. Está hecha con HTML, CSS y JavaScript "vanilla" (sin librerías), sigue el patrón **MVC** (Modelo-Vista-Controlador) y guarda los datos en el `localStorage` del navegador.

Práctica de la asignatura **Tecnologías Móviles y Web**.

## Cómo ejecutarla

No necesita instalación ni servidor. Basta con abrir `index.html` en el navegador (Chrome o Firefox).

### Configurar la API de The Movie Database (TMDb)

La búsqueda de películas usa la API REST de TMDb, que necesita una clave:

1. Crea una cuenta en [themoviedb.org](https://www.themoviedb.org/) y solicita una clave en *Ajustes → API*.
2. Copia `config.example.js` y llama a la copia `config.js` (en la misma carpeta que `index.html`).
3. Pega en `config.js` el **Token de acceso de lectura a la API** (empieza por `eyJ...`) en lugar de `PEGA_AQUI_TU_TOKEN`.

`config.js` está en `.gitignore`, así que la clave no se sube al repositorio. Si falta o no es válida, la vista de búsqueda lo indica con un mensaje.

**Versión publicada (GitHub Pages) o sin `config.js`:** al abrir *Buscar en TMDb* aparece un recuadro para pegar el token. Se guarda solo en el `localStorage` de ese navegador (clave `tmdb_token`), nunca en el repositorio, y se puede borrar con el botón *Olvidar token*. Si existe `config.js`, tiene prioridad y el recuadro no aparece.

## Funcionalidades

| Acción | Botón | Qué hace |
|---|---|---|
| **Index** | Volver | Muestra la lista de películas con su portada y su título. |
| **Show** | Ver | Muestra el título y el director de una película. |
| **New** | Añadir | Muestra el formulario para crear una película. |
| **Create** | Crear | Añade la nueva película al modelo y vuelve a la lista. |
| **Edit** | Editar | Muestra el formulario con los datos de la película seleccionada. |
| **Update** | Actualizar | Guarda los cambios de la película y vuelve a la lista. |
| **Delete** | Borrar | Elimina la película, después de pedir confirmación. |
| **Reset** | Restablecer | Devuelve el modelo a las tres películas iniciales. |
| **Search view** | Buscar en TMDb | Muestra la vista de búsqueda (campo de texto y botón). |
| **Search** | Buscar / tecla Enter | Valida el texto, consulta la API de TMDb y muestra los resultados. |
| **Add from API** | Añadir (en cada resultado) | Guarda la película en la lista local, con su director, póster, fecha y sinopsis. |
| **Save token** | Guardar token / tecla Enter | Guarda en el navegador el token de TMDb pegado (solo si no hay `config.js`). |
| **Clear token** | Olvidar token | Borra el token guardado en el navegador. |

## Estructura del proyecto

```
├── index.html        Aplicación completa (HTML, CSS y JavaScript)
├── config.example.js Plantilla para la clave de la API de TMDb
├── config.js         Tu clave de TMDb (no se sube al repositorio)
├── files/            Portadas de las películas iniciales e imagen por defecto
├── tests/            Tests automáticos de la práctica
└── README.md
```

## Organización del código (MVC)

Todo el código está en `index.html`, dentro de la etiqueta `<script>`:

- **Modelo:** un array de objetos `{ title, director, thumbnail }` guardado como JSON en `localStorage` con la clave `my_movies`. Si la clave no existe, se crea con las películas de `initial_movies`.
- **Vistas:** `indexView`, `showView`, `newView`, `editView`, `searchView` y `resultsView`. Generan el HTML que se inserta en `<div id="main">`.
- **Controladores:** `indexContr`, `showContr`, `newContr`, `createContr`, `editContr`, `updateContr`, `deleteContr`, `resetContr`, `searchViewContr`, `searchContr`, `addFromAPIContr`, `saveTokenContr` y `clearTokenContr`. Leen o modifican el modelo y muestran la vista correspondiente.
- **Router:** un único manejador del evento `click` sobre `document` que comprueba la clase del botón pulsado (`.show`, `.new`, `.search`, `.add-from-api`…) y llama al controlador adecuado. El índice de la película se pasa con el atributo `data-my-id`. Un manejador de `keydown` lanza la búsqueda al pulsar Enter en el campo de búsqueda.

### Integración con TMDb

- Las peticiones se hacen con `fetch` y `async/await` en la función `tmdbFetch`, con la clave en la cabecera `Authorization: Bearer ...`. Los errores se gestionan con `try/catch/finally` y se traducen a mensajes claros: sin clave, sin conexión, `401` (clave no válida), `404`, `429` y otros códigos de estado.
- `searchContr` valida el término (no vacío, máximo 100 caracteres, sin caracteres extraños), llama a `GET /search/movie` y muestra los resultados o un aviso si no hay ninguno. El botón se deshabilita mientras se busca.
- La búsqueda no devuelve el director, así que `addFromAPIContr` lo pide a `GET /movie/{id}/credits`. La película se guarda con la misma estructura que las creadas a mano (`title`, `director`, `thumbnail`), más los campos opcionales `release_date`, `overview` y `tmdb_id`, que la vista *Show* muestra si existen.
- Antes de añadir, se comprueba si la película ya está en la lista (por `tmdb_id` o, en las creadas a mano, por título) y se avisa en ese caso. El botón se deshabilita mientras se añade para evitar duplicados por doble clic.
- Los textos que llegan de la API se escapan antes de insertarlos en el HTML.
- Las tarjetas de resultados usan animaciones y transiciones CSS (`@keyframes`, `transition`, `transform`).

## Notas

- La clave de TMDb no se sube al repositorio, pero en una aplicación que solo tiene cliente el navegador sigue necesitándola para hacer las peticiones, así que cualquiera que use la página puede verla. Para ocultarla del todo haría falta un servidor intermedio. Por eso se usa el token de **solo lectura**.
- Las películas añadidas se guardan solo en el navegador donde se crean. Cada navegador, y cada forma de abrir la página (por ejemplo, el archivo directamente o la vista previa de VS Code), tiene su propio `localStorage`, por la política del mismo origen.
- El test de Reset de `tests/checks.js` busca la clave `mis_peliculas`, mientras que el enunciado pide `my_movies`. He mantenido `my_movies`, siguiendo las indicaciones del enunciado.
