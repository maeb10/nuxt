# Mi sitio web con Nuxt

## Dev

1- Clona el repositorio

2- Instala las dependencias con `bun install`

3- Clonar el archivo `.env.template` a `.env` y configura las variables de entorno necesarias.

4- Ejecutar `npx prisma migrate dev` para aplicar las migraciones de la base de datos.

5- Ejecutar `bun prismag` para generar el cliente de Prisma con los tipos y métodos basados en el `schema.prisma`

6- Ejecutar el seed con `bun seed`

7- Inicia el servidor de desarrollo con `bun dev`