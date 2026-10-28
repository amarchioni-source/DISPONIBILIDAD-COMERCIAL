# Disponibilidad comercial

Página para que los comerciales vean el volumen disponible por semana de faena y lo reserven para un cliente. Es un único archivo (`index.html`) que guarda los datos en Firebase Realtime Database, así que no necesita servidor propio.

## Cómo se usa

La página tiene cuatro pestañas:

- **Disponibilidad:** arriba, una tarjeta por época (Aéreos, Marítimos, Full levy) y un total, con las fechas de cada una. Cada tarjeta muestra el **total**, lo **asignado a clientes** y lo que está **sin cliente**, en general y por producto, con una barra de reparto donde cada cliente tiene su color. Abajo, la tabla por semana de faena: cada celda muestra total, con cliente y sin cliente, la barra y los clientes con sus toneladas. Al tocar una celda se abre el cuadro para **asignar a un cliente**: cliente, comercial y toneladas.
- **Reservas:** lista de todo lo asignado, con filtro por comercial y búsqueda por cliente, y el total de toneladas por cliente (por ejemplo M2000 o Fritz).
- **Faena y cortes:** las cabezas de cada semana (VA TP, VA UE, NV C/Kosher USA, NV S/Kosher, NV C/Kosher y las de 481: S/Kosher, Kosher USA y C/Kosher) y cómo se abren en cortes, como en la hoja PROY 481 y NH. Las cabezas se pueden pegar directo del Excel y los rendimientos por media res se pueden ajustar.
- **Carga de volumen:** donde se cargan a mano Menaker, R&L 481 y Sets 481, y se ven los volúmenes calculados de B. Ancho y R&L Hilton.

En cada celda, el número verde es lo que no tiene cliente asignado y el azul lo que sí. Si se asignó más que el total, la celda se marca en rojo. Todo está en toneladas y se actualiza en vivo para los que tengan la página abierta.

## Cómo se calcula

Las cabezas por semana se cargan en **Faena y cortes**. Para no tipearlas, en la hoja `1 - Detalle de Faena` del Excel seleccioná las filas de las semanas desde la columna **L hasta la W**, copiá y pegá en el panel **Cargar semanas desde el Excel**. Las semanas pegadas reemplazan las que ya estaban.

**Disponible Hilton** (a partir de NV C/Kosher = K y NV S/Kosher = L):

- B. Ancho Hilton = `(K × 0,35 + L) × 2,5 × 2 / 1000`
- R&L Hilton = `(K + L) × 7,7 × 2 / 1000`

**Cortes de 481** (a partir de S/Kosher = E y C/Kosher = F de 481):

- No kosher: `I = E + 0,4 × F` (el 40 % de los c/Kosher se rechaza). Kosher apto: `J = E + F − I`.
- Pecho `11,5 × 2 × I`, R&L `8 × 2 × I`, B. Ancho `2,5 × 2 × I`, Rueda `17 × 2 × I`. **14 Ctes** es la suma de esos cuatro.
- Kosher: R&L `J × 8 × 2` y Rueda `J × 17 × 2`.
- Garr/Tort/Braz `I × 10,5 × 2 + J × 3,45 × 2` y Cogote `I × 3,5 × 2`.

**Efecto del kosher** (tabla en Faena y cortes): por semana, el B. Ancho de Hilton que no cuenta por ser kosher (`K × 2,5 × 2 × 0,65`), las cabezas de 481 rechazadas y aptas, el R&L y la Rueda que salen como kosher, y los cortes que dejan de producirse (Pecho, B. Ancho, Cogote y la diferencia de Garr/Tort/Braz de las cabezas aptas).

Todo se muestra en toneladas. Las fórmulas y los factores son los de la planilla PROY 481 y NH para las semanas 44 a 04, y los factores se ajustan en el panel **Rendimientos por media res** (valen para todas las semanas). En la planilla, la semana 44 no aplica el rechazo, pero ese dato no cambia nada porque no tiene cabezas c/Kosher de 481. La planilla también calcula el volumen de no Hilton (NH), que no se replicó.

Menaker, R&L 481 y Sets 481 se cargan a mano. Stk Caj y Stk Q son informativos y no entran en los totales.

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

- `semanas/cabezas/<semana>/<campo>`: cabezas de cada semana. Campos: `vaTP`, `vaUE`, `nvCKU` (NV C/Kosher USA), `nvSK`, `nvCK`, `e481SK`, `e481KU` y `e481CK`.
- `semanas/parametros/<clave>`: factores de cálculo y rendimientos por media res.
- `semanas/celdas/<semana>/<producto>/kg`: volumen cargado a mano (Menaker, R&L 481, Sets 481 y Stk).
- `semanas/celdas/<semana>/<producto>/asig/<id>`: cada asignación a un cliente, con `cliente`, `comercial`, `kg` (en tn) y `ts`.

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
