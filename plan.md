# Plan de implementación — ColectionsChile

## Alcance

ColectionsChile será un catálogo web público para Hot Wheels y otros artículos de colección, con detalle de producto, estado de disponibilidad y comentarios públicos. La persona dueña tendrá un panel privado protegido por Manus OAuth para crear, editar y eliminar productos, gestionar fotografías y ajustar cantidades.

## Arquitectura

- **Frontend:** React + Wouter + Tailwind del starter `web-db-user`.
- **Backend:** Express + tRPC con procedimientos públicos para catálogo/comentarios y procedimientos `adminProcedure` para administración.
- **Persistencia:** MySQL gestionado con Drizzle. Tablas `products` y `comments`, además de `users` existente.
- **Imágenes:** almacenamiento administrado de Manus mediante `storagePut`; se guarda la URL estable `/manus-storage/...` en el producto.
- **Autenticación:** Manus OAuth existente. El rol admin se deriva del `MANUS_OWNER_OPEN_ID` del starter para que solo la cuenta propietaria pueda usar el panel.
- **Compartir:** rutas `/` para catálogo, `/producto/:id` para detalle, `/admin` para administración y `manus-routes.json` sincronizado.

## Estructura principal

- `client/src/pages/Home.tsx`: catálogo público y hero editorial.
- `client/src/pages/ProductDetail.tsx`: detalle, disponibilidad y comentarios.
- `client/src/pages/Admin.tsx`: dashboard privado, formulario de alta/edición y gestión de inventario.
- `client/src/index.css`: sistema visual ColectionsChile.
- `server/routers.ts`: procedimientos tRPC.
- `server/db.ts`: consultas de productos y comentarios.
- `drizzle/schema.ts`: tablas de negocio.
- `drizzle/*.sql`: migración aditiva.
- `client/public/manus-routes.json`: manifiesto de rutas.

## Dirección visual

- **Movimiento:** editorial de colección / brutalismo suave, combinando superficies blancas, tipografía negra de alto contraste y acentos verde lima.
- **Principios:** claridad de inventario, energía de hobby, ritmo asimétrico y controles directos.
- **Color:** blanco como vitrina limpia; negro como base de archivo y confianza; verde ácido como firma de disponibilidad, acción y comunidad.
- **Layout:** barra superior compacta, hero de dos columnas con bloque de color, catálogo en tarjetas apiladas y paneles con bordes marcados en lugar de una cuadrícula genérica.
- **Elementos firma:** monograma `CC` dentro de un disco verde, etiquetas tipo ficha de inventario y una línea “archivo vivo” que conecta catálogo y comentarios.
- **Interacción:** los estados “Disponible” y “Agotado” son visibles sin abrir el detalle; los comentarios se envían con nombre y texto; el administrador ve cambios inmediatamente tras guardar.
- **Animación:** entradas suaves con `fade-up`, hover con desplazamiento de 2px y transiciones cortas, sin animaciones que distraigan del producto.
- **Tipografía:** `Space Grotesk` para titulares y marca; `DM Sans` para cuerpo y controles, con fallback sans-serif.
- **Esencia:** “La vitrina chilena para encontrar, conversar y reservar piezas de colección.” Personalidad: precisa, entusiasta, cercana.
- **Voz:** titulares cortos y de coleccionista. Ejemplos: “Piezas con historia. Stock real.” / “¿Lo viste disponible? Déjanos tu comentario.”
- **Wordmark:** `CC` monograma encajado en un círculo verde, seguido de `colections` en minúsculas y `CHILE` en bloque negro.
- **Color propio:** verde `#b7f34a`.

## Decisiones de producto

- La administración requiere login OAuth real; no se simula autenticación.
- Los comentarios son públicos y se guardan con nombre, texto y fecha; el administrador puede eliminarlos desde su panel.
- La cantidad `0` se interpreta como agotado; cualquier valor mayor se muestra como disponible.
- Se incluye contenido inicial de demostración en la interfaz solo como estado vacío amigable si la base no tiene productos; no se inserta automáticamente para no contaminar el inventario real.
- El formulario de administración admite URL de imagen y carga local; la carga local se transfiere al almacenamiento gestionado desde el servidor.
