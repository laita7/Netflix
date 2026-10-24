# My Little Netflix

Aplicación web cliente que funciona como una pequeña base de datos de películas. Está hecha con HTML, CSS y JavaScript "vanilla" (sin librerías), sigue el patrón **MVC** (Modelo-Vista-Controlador) y guarda los datos en el `localStorage` del navegador.

Práctica de la asignatura **Tecnologías Móviles y Web**.

## Cómo ejecutarla

No necesita instalación ni servidor. Basta con abrir `index.html` en el navegador (Chrome o Firefox).

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

## Estructura del proyecto

```
├── index.html        Aplicación completa (HTML, CSS y JavaScript)
├── files/            Portadas de las películas iniciales e imagen por defecto
├── tests/            Tests automáticos de la práctica
└── README.md
```

## Organización del código (MVC)

Todo el código está en `index.html`, dentro de la etiqueta `<script>`:

- **Modelo:** un array de objetos `{ title, director, thumbnail }` guardado como JSON en `localStorage` con la clave `my_movies`. Si la clave no existe, se crea con las películas de `initial_movies`.
- **Vistas:** `indexView`, `showView`, `newView` y `editView`. Generan el HTML que se inserta en `<div id="main">`.
- **Controladores:** `indexContr`, `showContr`, `newContr`, `createContr`, `editContr`, `updateContr`, `deleteContr` y `resetContr`. Leen o modifican el modelo y muestran la vista correspondiente.
- **Router:** un único manejador del evento `click` sobre `document` que comprueba la clase del botón pulsado (`.show`, `.new`, `.delete`…) y llama al controlador adecuado. El índice de la película se pasa con el atributo `data-my-id`.

## Notas

- Las películas añadidas se guardan solo en el navegador donde se crean. Cada navegador, y cada forma de abrir la página (por ejemplo, el archivo directamente o la vista previa de VS Code), tiene su propio `localStorage`, por la política del mismo origen.
- El test de Reset de `tests/checks.js` busca la clave `mis_peliculas`, mientras que el enunciado pide `my_movies`. He mantenido `my_movies`, siguiendo las indicaciones del enunciado.
