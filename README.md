# Comprobación de PC Vitalinux

Aplicación de escritorio para verificar el estado de equipos de trabajo en VitaLinux, desarrollada con **Tauri** (Rust + Web).

![alt text](image.png)

## Características

-   Formulario intuitivo de verificación de componentes
-   Exportación automática de informes en formato JSON
-   Diseño responsive adaptado a cualquier pantalla
-   Rendimiento nativo gracias a Tauri
-   Autostart al inicio de sesión en VitaLinux

## Componentes Verificables

-   **Pantalla** (Obligatorio)
-   **Teclado** (Obligatorio)
-   **Ratón** (Opcional)
-   **Batería** (Opcional)
-   **Otros** (Opcional)

## Instalación en VitaLinux/Ubuntu

Descarga el paquete `.deb` desde la [página de Releases](https://github.com/deleyva/vx-dga-pc-check-form/releases/latest) e instálalo:

```bash
sudo dpkg -i vx-dga-pc-check-form_<VERSION>_amd64.deb
sudo apt install -f  # Instalar dependencias si es necesario
```

La aplicación se iniciará automáticamente al hacer login gracias al fichero `.desktop` instalado en `/etc/xdg/autostart/`.

### Dependencias del Sistema

```bash
sudo apt install libwebkit2gtk-4.0-37 libgtk-3-0
```

## Releases automáticos (CI/CD)

El proyecto usa GitHub Actions para generar releases automáticamente.

### Crear un nuevo release

Usa el comando `just release <version>` para automatizar el proceso:

```bash
just release 1.0.21
```

O sigue los pasos manualmente:

```bash
# 1. Bump versión en package.json + package-lock.json
npm version 1.0.21 --no-git-tag-version

# 2. Bump versión en tauri.conf.json (npm no lo toca)
sed -i 's/"version": "1.0.16"/"version": "1.0.21"/' src-tauri/tauri.conf.json

# 3. Commit, tag y push
git add package.json package-lock.json src-tauri/tauri.conf.json
git commit -m "Bump version to v1.0.21"
git tag v1.0.21
git push && git push origin v1.0.21
```

GitHub Actions construye el `.deb` y crea el release con la versión correcta.

> **Importante:** La versión en `package.json` y `tauri.conf.json` debe coincidir con el tag. Si no, el título del release no coincidirá con el tag.

Alternativamente puedes usar `./release.sh` que automatiza todo el proceso (bump, build, tag, push).

## Desarrollo

```bash
# Instalar dependencias
npm install

# Ejecutar en modo desarrollo
npm run dev

# Construir para producción
npm run build
```

## Estructura del JSON generado

```json
{
  "timestamp": "2024-11-08T10:30:00.000Z",
  "empresa": "VITALINUX",
  "tipo_verificacion": "equipos_escritorio",
  "verificacion_equipos": {
    "pantalla": {
      "estado": "correcto|defectuoso",
      "problema": "descripción del problema",
      "obligatorio": true
    }
  },
  "resumen": {
    "total_componentes": 5,
    "componentes_correctos": 4,
    "componentes_defectuosos": 1,
    "equipo_operativo": false,
    "requiere_atencion": true
  }
}
```

## Configuración de la API

### Variable de Entorno VX_API_URL

La variable veine VX_API_URL viene configurada por defecto al instalar la aplicación con el valor \`<http://servidor.vx:3001/v1/report>.

Se puede sobrescribir con una variable de entorno `VX_API_URL` en el sistema.

Para un usuario concreto:

```bash
VX_API_URL="http://192.168.1.105:3001/v1/report" vx-dga-pc-check-form
```

Para configurarlo de forma permanente para **todos los usuarios del sistema**:

```bash
# Crear fichero de entorno global
sudo tee /etc/environment.d/vx-dga-pc-check-form.conf << 'EOF'
VX_API_URL="http://192.168.1.105:3001/v1/report"
EOF
```

> Los usuarios necesitan cerrar sesión y volver a entrar para que surta efecto.

Alternativa con `/etc/environment` (compatible con sistemas sin `environment.d`):

```bash
# Añadir al final de /etc/environment
echo 'VX_API_URL="http://192.168.1.105:3001/v1/report"' | sudo tee -a /etc/environment
```

### Modo Dry-Run (Testing)

```bash
vx-dga-pc-check-form --dry-run
```

## Comandos del Sistema

```bash
# Obtener CID
migasfree-cid

# Obtener usuario gráfico
vx-usuario-grafico
```
