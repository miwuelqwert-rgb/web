# ColectionsChile — instalación y privacidad

## Qué contiene

ColectionsChile es un catálogo de productos con fotos, descripción, cantidad disponible, comentarios, solicitudes de producto y panel privado de administración. El catálogo ahora funciona en modo **privado por enlace**: una persona solo puede ver el stock si abre el enlace que contiene el token de acceso.

## Qué instalar

En un servidor o computador nuevo instala:

1. **Node.js 22 LTS** o una versión compatible con Node 20+.
2. **pnpm 10.18.0**, con `corepack enable` y `corepack prepare pnpm@10.18.0 --activate`.
3. **MySQL 8+** o un proveedor MySQL compatible. La variable `DATABASE_URL` debe apuntar a esa base.
4. Opcionalmente **Git** y **Docker**, si quieres desplegar usando el `Dockerfile` incluido.

No necesitas instalar React, Vite, Drizzle ni Express manualmente: todas las dependencias se instalan con `pnpm install`.

## Instalación local

```bash
unzip colectionschile.zip
cd colectionschile
cp .env.example .env
corepack enable
corepack prepare pnpm@10.18.0 --activate
pnpm install
pnpm db:push
pnpm dev
```

Luego abre `http://localhost:3000/?acceso=TU_CATALOG_ACCESS_TOKEN`.

Para producción:

```bash
pnpm build
NODE_ENV=production PORT=3000 pnpm start
```

El servidor escucha en `0.0.0.0:3000`. Si usas un dominio, coloca un reverse proxy HTTPS delante (por ejemplo Nginx, Caddy, Railway, Render, Fly.io o Docker) y conserva `PORT=3000` dentro del contenedor.

## Cómo funciona el acceso privado

El servidor protege las rutas HTML y la API tRPC. El enlace tiene esta forma:

```text
https://tu-dominio.cl/?acceso=TU_CATALOG_ACCESS_TOKEN
```

Al abrirlo, se guarda una cookie HttpOnly durante 30 días y la URL queda limpia. Las personas que no tengan la cookie o el token reciben un error 404/403 y no pueden consultar el stock mediante la API.

**Importante:** cambia `CATALOG_ACCESS_TOKEN` por un valor largo y difícil de adivinar antes de compartir el enlace. Si cambias el token, los enlaces anteriores dejan de funcionar. Comparte el enlace completo, incluyendo `?acceso=...`, solo con las personas autorizadas. Este modo es privado por enlace, no una cuenta individual para cada visitante: cualquier persona que reenvíe el enlace podrá entrar.

## Panel de administración

Abre el panel con el enlace privado:

```text
https://tu-dominio.cl/admin?acceso=TU_CATALOG_ACCESS_TOKEN
```

El panel sigue exigiendo autenticación Manus OAuth y solo la cuenta cuyo `openId` coincide con `OWNER_OPEN_ID` queda como administradora. Las demás cuentas no pueden editar inventario.

## Notificaciones de solicitudes

Cuando alguien pulsa **Solicitar producto**, la solicitud se guarda en `comments` con `isRequest=1` y se envía una notificación al dueño mediante el servicio de notificaciones de Manus. También aparece en **Comentarios y solicitudes** dentro de `/admin`.

En una instalación fuera de Manus, las notificaciones Manus requieren mantener configurados `MANUS_API_URL` y `MANUS_API_KEY`. Si no se configura ese servicio, la solicitud se guarda igualmente en la base y se puede revisar desde el panel.

## Variables necesarias

Copia `.env.example` a `.env` y completa `DATABASE_URL`, `CATALOG_ACCESS_TOKEN`, `MANUS_PROJECT_ID`, `MANUS_JWT_SECRET`, `MANUS_OAUTH_API_URL`, `MANUS_API_URL`, `MANUS_API_KEY` y `OWNER_OPEN_ID`. No subas `.env` al repositorio ni lo incluyas en un ZIP compartido.
