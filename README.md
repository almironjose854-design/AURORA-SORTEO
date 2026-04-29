# Aurora Sorteo

Formulario y panel administrativo para registrar participantes de un sorteo con `Node.js` y persistencia en archivos JSON.

## Lo que hace

- Recibe registros desde el formulario publico.
- Guarda los datos en `entries.json`.
- Muestra los registros desde un panel admin.
- Exporta datos en `JSON` y `CSV`.
- Funciona en local y en Fly.io con volumen persistente.

## Requisitos

- Node.js 18 o superior
- npm
- Cuenta en Fly.io
- Repositorio en GitHub

## Ejecutar en local

1. Instala dependencias:

```bash
cd server
npm install
```

2. Vuelve a la raiz y arranca:

```bash
npm start
```

3. Abre:

- Formulario: `http://localhost:3000`
- Panel: `http://localhost:3000/admin`

Credenciales por defecto:

- Usuario: `admin`
- Contrasena: `admin123`

## Preparar GitHub

1. Sube el proyecto al repositorio.
2. No subas `server/node_modules` porque ya esta cubierto por `.gitignore`.

## Deploy en Fly.io

Antes del primer deploy:

1. Cambia el nombre de la app en `fly.toml` por uno unico.
2. Instala `flyctl` y haz login:

```bash
fly auth login
```

3. Crea la app si aun no existe:

```bash
fly apps create TU_NOMBRE_DE_APP
```

4. Crea el volumen persistente en la misma region del `fly.toml`:

```bash
fly volumes create aurora_data --region gru --size 1 -a TU_NOMBRE_DE_APP
```

5. Configura credenciales seguras:

```bash
fly secrets set ADMIN_USER=admin ADMIN_PASSWORD=tu-clave-segura -a TU_NOMBRE_DE_APP
```

6. Despliega:

```bash
fly deploy -a TU_NOMBRE_DE_APP
```

## Probar en Fly.io

Despues del deploy:

1. Abre la URL publica de Fly.
2. Carga un registro de prueba.
3. Entra al panel admin y verifica que aparezca.
4. Reinicia la app y vuelve a comprobar:

```bash
fly machine list -a TU_NOMBRE_DE_APP
fly machine restart MAQUINA_ID -a TU_NOMBRE_DE_APP
```

Si el registro sigue ahi, el volumen esta funcionando bien.

## Deploy automatico desde GitHub

El workflow ya esta creado en `.github/workflows/fly.yml`.

Solo falta agregar en GitHub el secret:

- `FLY_API_TOKEN`

Puedes crear el token con:

```bash
fly tokens create deploy -x 999999h
```

Cuando hagas `push` a `main`, GitHub Actions intentara desplegar automaticamente.

## Importante para este proyecto

- En Fly.io debes usar `1` sola Machine mientras guardes en JSON.
- El volumen guarda datos persistentes, pero no sincroniza varias Machines.
- Si despues quieres escalar a varias instancias, conviene migrar a SQLite o PostgreSQL.
