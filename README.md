# Suivi ADV — Tableau de bord

Dashboard de seguimiento de actividades del equipo ADV (Solenne · Martina · Diana).
Frontend estático en GitHub Pages + base de datos real en Google Sheets vía Apps Script.

- Acceso con **código de 3 dígitos**, sesión de **8 horas**.
- Registro rápido, tabla filtrable, **Kanban** por estado, **gráficos**.
- Cada **N° UPS** es un enlace directo al estado del envío.
- **Journal** de trazabilidad: quién cambió qué y cuándo.

---

## Cómo funciona (importante para la seguridad)

El repositorio de GitHub es público, así que **la contraseña NUNCA se guarda en el código**.
Vive dentro de Apps Script. El flujo es:

```
Navegador (index.html en GitHub Pages)
     │   manda el código →
     ▼
Apps Script (valida contra la contraseña guardada en el servidor)
     │   lee/escribe →
     ▼
Google Sheet  →  hoja "Suivi" (datos) + hoja "Journal" (historial)
```

---

## Instalación (una sola vez, ~10 minutos)

### 1. Crear la hoja de Google
1. Ve a [sheets.new](https://sheets.new) y crea una hoja nueva. Llámala **Suivi ADV**.
2. Menú **Extensions → Apps Script**.

### 2. Pegar el backend
1. En el editor de Apps Script, borra el contenido de `Code.gs`.
2. Pega **todo** el contenido del archivo `Code.gs` de este proyecto.
3. Guarda (💾).

### 3. Crear las hojas y el secreto
1. Arriba, elige la función **`setup`** en el desplegable y pulsa **▶ Ejecutar**.
2. Autoriza los permisos que pida (es tu propia cuenta).
   Esto crea las hojas **Suivi** y **Journal** y genera un `SECRET` aleatorio.

### 4. Definir la contraseña
1. En Apps Script: ⚙️ **Configuración del proyecto** → **Propiedades del script** → **Editar propiedades**.
2. Añade una propiedad:
   - Nombre: `PASSWORD`
   - Valor: tus **3 dígitos** (ej. `740`)
3. Guarda. (No compartas este valor por escrito en el repo.)

### 5. Publicar el Web App
1. Arriba a la derecha: **Implementar → Nueva implementación**.
2. Tipo: **Aplicación web**.
3. Configura:
   - **Ejecutar como:** *Yo* (tu cuenta).
   - **Quién tiene acceso:** *Cualquier persona*.
     > Es seguro: el acceso real está protegido por tu contraseña de 3 dígitos.
4. **Implementar** y **copia la URL** (termina en `/exec`).

### 6. Conectar el frontend
1. Abre `index.html` y busca al inicio del `<script>`:
   ```js
   const CONFIG = { API_URL: "" };
   ```
2. Pega tu URL entre las comillas:
   ```js
   const CONFIG = { API_URL: "https://script.google.com/macros/s/AKfy.../exec" };
   ```
3. Guarda.

> Mientras `API_URL` esté vacío, la app funciona en **modo demo** (datos de ejemplo
> guardados solo en tu navegador, contraseña de prueba `123`). Útil para probar antes de conectar.

---

## Subir a GitHub Pages

```bash
git init
git add index.html README.md .gitignore
git commit -m "Suivi ADV: tableau de bord initial"
git branch -M main
git remote add origin https://github.com/TU_USUARIO/suivi-adv.git
git push -u origin main
```

> **No subas `Code.gs`** al repo público si prefieres no mostrar la lógica del backend.
> No es un secreto (la contraseña no está ahí), pero el `.gitignore` ya lo excluye por defecto.

Luego, en GitHub: **Settings → Pages → Source: `main` / root → Save**.
En 1–2 minutos tendrás la URL pública `https://TU_USUARIO.github.io/suivi-adv/`.

---

## Uso diario

- **Registrar:** rellena la barra superior y pulsa `Entrée` o **＋ Ajouter**.
  El responsable es quien esté seleccionado en «Qui saisit ?».
- **Editar / borrar:** clic en cualquier fila (o tarjeta del Kanban).
- **Cambiar estado rápido:** arrastra la tarjeta entre columnas del Kanban.
- **Filtrar:** por persona, tipo, estado, rango de fechas y búsqueda libre.
  El contador «résultats» y los gráficos se actualizan con los filtros.
  → Para «¿cuántas commandes a X cliente por día?»: filtra Type = *Commandes*,
    escribe el cliente y mira la pestaña **Graphiques** (actions par jour / top clients).
- **Tracking:** el N° UPS se convierte en enlace directo al estado del envío.

---

## Cambiar la contraseña

Apps Script → Propiedades del script → edita `PASSWORD`. Efecto inmediato,
sin tocar el código ni redeployar.

## Añadir o cambiar tipos / estados

En `index.html`, edita las listas `TYPES` y `STATUTS` al inicio del `<script>`.
La opción **« Autre… »** ya permite valores libres puntuales sin tocar el código.

---

## Solución de problemas

| Síntoma | Causa probable | Solución |
|---|---|---|
| «Code incorrect» siempre | `PASSWORD` no definido | Paso 4 de la instalación |
| «Chargement impossible» | URL mal pegada o no redeployada | Revisa `API_URL`; vuelve a implementar |
| Cambios no se guardan tras editar `Code.gs` | El Web App usa la versión antigua | **Implementar → Gestionar → Editar → Nueva versión** |
| No aparece nada | Sigues en modo demo | Rellena `API_URL` |
