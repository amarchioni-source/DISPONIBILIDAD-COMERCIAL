# Disponibilidad comercial

Tabla editable para que los comerciales vean el volumen disponible por semana y lo asignen a clientes. Es un único archivo (`index.html`) que guarda los datos en Firebase Realtime Database, así que no necesita servidor propio.

## Qué hace

- Tabla por semana de faena con las columnas B. Ancho Hilton, R&L Hilton (con M2000 y Fritz), Menaker, R&L 481 y Sets 481. Volúmenes en toneladas.
- **El disponible de B. Ancho Hilton y R&L Hilton se calcula solo** a partir de las cabezas NV (no vendidas) de cada semana, con las fórmulas de la planilla PROY 481 y NH.
- Detalle de clientes por celda (cliente, comercial y tn), con indicador de color: verde si todo está asignado, amarillo si falta y rojo si se pasa.
- **Saldo acumulado** en B. Ancho Hilton y R&L Hilton: arrastra lo que sobra o falta de una semana a la siguiente.
- Totales por época (Aéreos, Marítimos, Full levy) y total general arriba, con el rango de fechas de cada época.
- Cambios en vivo para todos los que tengan la página abierta.

## Cómo se calcula el disponible

Se cargan, por semana de faena, las cabezas **NV c/Kosher** (K) y **NV s/Kosher** (L).

- Disponible B. Ancho Hilton = `(K × 0,35 + L) × 2,5 × 2 / 1000`
- Disponible R&L Hilton = `(K + L) × 7,7 × 2 / 1000`

Son las fórmulas de la fila de Sem 44 de la hoja `PROY 481 y NH`. Los factores (7,7 kg de R&L, 2,5 kg de B. Ancho, 0,35 y 2 medias por cabeza) se pueden cambiar en el panel **Factores de cálculo** de la página y valen para todas las semanas.

Saldo acumulado de una semana = saldo anterior + disponible − asignado. Arranca con el **saldo inicial** configurable en el mismo panel (0,7 tn para B. Ancho Hilton, el valor que arrastra la planilla en Sem 44, y 0 para R&L Hilton).

## Cómo se reparte

- **B. Ancho Hilton:** el botón Clientes asigna lo calculado a clientes.
- **R&L Hilton:** el disponible calculado se reparte entre **M2000** y **Fritz**. Cada una tiene su botón Clientes, y el disponible muestra cuánto queda libre.
- **Menaker, R&L 481 y Sets 481:** el disponible se carga a mano y se asigna con el botón Clientes.
- **Stk Caj y Stk Q:** se cargan a mano y no entran en los totales.

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
- `semanas/parametros/<clave>`: factores de cálculo (`rl`, `ba`, `f`, `m`, `sIniA`, `sIniR`).
- `semanas/celdas/<semana>/<columna>/kg`: disponible cargado a mano (Menaker, R&L 481, Sets 481 y Stk).
- `semanas/celdas/<semana>/<columna>/asig/<id>`: cada asignación, con `cliente`, `comercial`, `kg` (en tn) y `ts`.

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
