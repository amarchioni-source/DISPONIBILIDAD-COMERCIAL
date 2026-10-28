# Semanas de faena y embarque

Tabla editable para que los comerciales completen el volumen disponible por semana y lo asignen a clientes. Es un único archivo (`index.html`) que guarda los datos en Firebase Realtime Database, así que no necesita servidor propio.

## Qué hace

- Tabla por semana de faena con las columnas B. Ancho Hilton, R&L Hilton (M2000 y Fritz), Menaker, R&L 481 y Sets 481.
- Totales por época (Aéreos, Marítimos, Full levy) y total general arriba, con el rango de fechas de cada época.
- Detalle de clientes por celda (cliente, comercial y kg), con indicador de color: verde si todo está asignado, amarillo si falta y rojo si se pasa.
- Cambios en vivo para todos los que tengan la página abierta.

## Estructura del proyecto

```
semanas-comerciales/
├── index.html                    La aplicación completa
├── database.rules.example.json   Reglas de ejemplo para Firebase
├── render.yaml                   Configuración opcional para Render
├── .gitignore
└── README.md
```

## Puesta en marcha

### 1. Firebase (proyecto de menudencias, ruta nueva)

1. En la consola de Firebase, entrá a **Authentication > Users** y creá un usuario nuevo (correo y contraseña) para los comerciales. Verificá que el método **Correo y contraseña** esté habilitado.
2. Entrá a **Realtime Database > Reglas**. Tomá como base `database.rules.example.json`, reemplazá `CORREO_MENUDENCIAS` y `CORREO_SEMANAS` por los correos reales y compará con las reglas que ya tenés para no perder otras rutas. Después publicá.

Los datos se guardan en `semanas/celdas/<semana>/<columna>`:

- `kg`: volumen disponible de la celda.
- `asig/<id>`: cada asignación, con `cliente`, `comercial`, `kg` y `ts`.

### 2. GitHub

Desde la web:

1. Creá un repositorio nuevo (por ejemplo `semanas-comerciales`).
2. Entrá a **Add file > Upload files**, arrastrá el contenido de esta carpeta y confirmá con **Commit changes**.

Con la terminal, dentro de la carpeta:

```bash
git init
git add .
git commit -m "Semanas de faena y embarque"
git branch -M main
git remote add origin https://github.com/TU_USUARIO/semanas-comerciales.git
git push -u origin main
```

### 3. Render

1. En Render, elegí **New > Static Site** y conectá el repositorio.
2. Dejá vacío el **Build Command**.
3. En **Publish Directory** escribí `.` y creá el sitio.

Render publica una dirección web que se comparte con los comerciales. Cada cambio que subas a GitHub se vuelve a publicar solo.

## Actualizar la aplicación

Editá `index.html` en GitHub (o subí la versión nueva) y confirmá el cambio. Render redespliega automáticamente. Los datos cargados no se pierden, porque viven en Firebase.

## Notas de seguridad

- La configuración de Firebase dentro de `index.html` (incluida la `apiKey`) es pública por diseño. La protección de los datos está en las reglas de la base y en el usuario de ingreso, no en ocultar esa clave.
- Con las reglas del ejemplo, cada usuario solo accede a su propia ruta. Si compartís el usuario de las semanas, conviene cambiarle la contraseña cada tanto.
