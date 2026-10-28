# 🔄 Sincronización Automática entre Repositorios con GitHub Actions

## 🎯 Problema que Soluciona

Al trabajar en proyectos alojados en organizaciones de GitHub (educativas, de cursos o de empresas), es habitual enfrentarse a los siguientes problemas:

* **Pérdida de trabajo al finalizar el curso/proyecto:** Las organizaciones suelen borrar, ocultar o archivar sus repositorios al terminar el periodo lectivo o contrato, eliminando tu portafolio personal.
* **Falta de una copia de seguridad propia:** Trabajar únicamente en la organización te deja sin un respaldo privado que dependa 100% de ti.
* **Riesgo por pruebas intermedias:** Subir cambios inacabados a la organización puede entorpecer las entregas oficiales.
* **Proceso manual e ineficiente:** Mantener dos repositorios sincronizados mediante descargas y resubidas manuales genera errores y pérdida de tiempo.

**Solución:** Este flujo de trabajo te permite desarrollar de forma independiente en tu **repositorio privado personal**. Cada vez que realices un `push`, una automatización en la nube copiará instantáneamente todo tu código e historial al repositorio oficial de la organización.

---

## 📋 Requisitos Previos

1. Un **repositorio privado personal** en tu cuenta de GitHub.
2. Un **repositorio de destino** dentro de la organización con permisos de escritura.

---

## 🚀 Guía de Configuración Paso a Paso

### 1. Generar un Personal Access Token (PAT)

El token le da permiso a GitHub Actions para escribir en el repositorio de la organización en tu nombre.

1. Ve a tu cuenta de GitHub > **Settings** (esquina superior derecha).
2. Entra en **Developer settings** > **Personal access tokens** > **Tokens (classic)**.
3. Haz clic en **Generate new token** > **Generate new token (classic)**.
4. Asigna un nombre en **Note** (ejemplo: `Sincronizacion-Organizacion`).
5. Marca las siguientes dos casillas de permisos (**scopes**):
   * `repo` (Acceso completo a repositorios privados y públicos).
   * `workflow` (Permiso para actualizar flujos de trabajo de GitHub Actions).
6. Haz clic en **Generate token**.
7. **Copia el código generado (empieza por `ghp_`) y guárdalo.**

---

### 2. Guardar el Token como Secreto en tu Repositorio Privado

1. Ve a tu **repositorio privado personal**.
2. Entra en **Settings** > **Secrets and variables** > **Actions**.
3. Haz clic en **New repository secret**.
4. Rellena los datos:
   * **Name:** `ORG_TOKEN`
   * **Secret:** Pega el token copiado en el paso anterior.
5. Haz clic en **Add secret**.

---

### 3. Crear el Flujo de Trabajo (Workflow)

1. En la raíz de tu repositorio privado, crea la ruta de carpetas `.github/workflows/`.
2. Dentro de `workflows`, crea el archivo `sync.yml`.
3. Pega el siguiente código en el archivo `.github/workflows/sync.yml`:ç

```yaml
name: Sincronizar con Organizacion

on:
  push:
    branches:
      - main

jobs:
  sync:
    if: github.repository_owner == 'TU_USUARIO_GITHUB'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
          persist-credentials: false

      - name: Enviar a la Organizacion
        run: |
          git config --global user.name "TU_USUARIO_GITHUB"
          git config --global user.email "TU_EMAIL@users.noreply.github.com"
          git remote add org https://TU_USUARIO_GITHUB:${{ secrets.ORG_TOKEN }}@[github.com/NOMBRE_ORGANIZACION/NOMBRE_REPOSITORIO.git](https://github.com/NOMBRE_ORGANIZACION/NOMBRE_REPOSITORIO.git)
          git push org HEAD:main --force


###📝 3.1 Campos a modificar en sync.yml
1. Antes de guardar el archivo, sustituye los valores del código por tus datos reales:
2. TU_USUARIO_GITHUB: Tu nombre de usuario exacto en GitHub (en la línea if, en user.name y en la URL).
3. TU_EMAIL: Tu correo electrónico registrado en GitHub (en user.email).
4. NOMBRE_ORGANIZACION: Nombre exacto de la organización de GitHub de destino.
5. NOMBRE_REPOSITORIO: Nombre exacto del repositorio receptor dentro de la organización.
6. main: Si la rama principal de tu repositorio o la del destino se llama master, cámbialo en branches: (línea 6) y en git push org HEAD:main (última línea).



