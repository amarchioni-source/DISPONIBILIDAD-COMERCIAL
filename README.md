# Disponibilidad comercial

Página para que los comerciales vean el volumen disponible por semana de faena y lo reserven para un cliente. Es un único archivo (`index.html`) que guarda los datos en Firebase Realtime Database, así que no necesita servidor propio.

## Cómo se usa

La página tiene tres pestañas:

- **Disponibilidad:** arriba, una tarjeta por época (Aéreos, Marítimos, Full levy) y un total, con las fechas de cada una. Abajo, la tabla por semana de faena con el volumen libre de cada producto. Al tocar una celda se abre el cuadro para **reservar a un cliente**: cliente, comercial y toneladas.
- **Reservas:** lista de todo lo reservado, con filtro por comercial y búsqueda por cliente, y el total de toneladas por cliente (por ejemplo M2000 o Fritz).
- **Carga de volumen:** donde se cargan las cabezas de cada semana, el volumen de Menaker, R&L 481 y Sets 481, y los factores de cálculo.

Colores de cada celda: verde si queda volumen libre, gris si está todo reservado y rojo si se reservó de más. Todo está en toneladas y se actualiza en vivo para los que tengan la página abierta.

## Cómo se calcula el disponible

Se cargan, por semana de faena, las cabezas **NV c/Kosher** (K) y **NV s/Kosher** (L). El resto sale de las fórmulas de la planilla PROY 481 y NH:

- Disponible B. Ancho Hilton = `(K × 0,35 + L) × 2,5 × 2 / 1000`
- Disponible R&L Hilton = `(K + L) × 7,7 × 2 / 1000`

Los factores (7,7 kg de R&L, 2,5 kg de B. Ancho, 0,35 y 2 medias por cabeza) se cambian en la pestaña **Carga de volumen** y valen para todas las semanas. Menaker, R&L 481 y Sets 481 se cargan a mano. Stk Caj y Stk Q son informativos y no entran en los totales.

## Estructura del proyecto

```
semanas-comerciales/
├── index.html                    La aplicación completa
├── database.rules.example.json   Reglas de ejemplo para Firebase
├── render.yaml                   Configuración opcional para Render
├── .gitignore
└── README.md
```

## Datos en Firebase

Todo vive bajo la ruta `semanas`:

- `semanas/cabezas/<semana>/nvCK` y `nvSK`: cabezas NV de cada semana.
- `semanas/parametros/<clave>`: factores de cálculo (`rl`, `ba`, `f`, `m`).
- `semanas/celdas/<semana>/<producto>/kg`: volumen cargado a mano (Menaker, R&L 481, Sets 481 y Stk).
- `semanas/celdas/<semana>/<producto>/asig/<id>`: cada reserva, con `cliente`, `comercial`, `kg` (en tn) y `ts`.

Los productos son `ancho` (B. Ancho Hilton), `rlh` (R&L Hilton), `menaker`, `rl481` y `sets481`.

## Puesta en marcha

### 1. Firebase (proyecto de menudencias, ruta nueva)

1. En la consola de Firebase, entrá a **Authentication > Users** y creá un usuario nuevo (correo y contraseña) para los comerciales. Verificá que el método **Correo y contraseña** esté habilitado.
2. Entrá a **Realtime Database > Reglas**. Tomá como base `database.rules.example.json`, reemplazá `CORREO_MENUDENCIAS` y `CORREO_SEMANAS` por los correos reales y compará con las reglas que ya tenés para no perder otras rutas (por ejemplo `prioridades`). Después publicá.

### 2. GitHub

Desde la web:

1. Creá un repositorio nuevo (por ejemplo `DISPONIBILIDAD-COMERCIAL`).
2. Entrá a **Add file > Upload files**, subí los archivos y confirmá con **Commit changes**.

Con la terminal, dentro de la carpeta:

```bash
git init
git add .
git commit -m "Disponibilidad comercial"
git branch -M main
git remote add origin https://github.com/TU_USUARIO/DISPONIBILIDAD-COMERCIAL.git
git push -u origin main
```

### 3. Render

1. En Render, elegí **New > Static Site** y conectá el repositorio.
2. Dejá vacío el **Build Command**.
3. En **Publish Directory** escribí `./` y creá el sitio.

Cada cambio que subas a GitHub se vuelve a publicar solo.

## Actualizar la aplicación

Editá `index.html` en GitHub (o subí la versión nueva) y confirmá el cambio. Render redespliega automáticamente. Los datos cargados no se pierden, porque viven en Firebase.

## Notas de seguridad

- La configuración de Firebase dentro de `index.html` (incluida la `apiKey`) es pública por diseño. La protección de los datos está en las reglas de la base y en el usuario de ingreso, no en ocultar esa clave.
- Con las reglas del ejemplo, cada usuario solo accede a su propia ruta. Si compartís el usuario de las semanas, conviene cambiarle la contraseña cada tanto.
