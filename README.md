# Proyecto: 
# Generación Automática de Documentación Java con GitHub Actions

## Descripción

Este proyecto demuestra cómo generar automáticamente la documentación JavaDoc de un proyecto Java y convertirla en formatos HTML y PDF, usando un flujo de trabajo de GitHub Actions.

---

# a) Herramientas usadas y comandos ejecutados

**Herramientas principales:**
- **Java** → Lenguaje del proyecto y base para Javadoc.
- **Javadoc** → Generador de documentación en HTML.
- **wkhtmltopdf** → Conversor de HTML a PDF.
- **GitHub Actions** → Automatización con workflows.
- **Linux (Ubuntu-latest)** → Entorno de ejecución del workflow.

### Comandos usados en el workflow:

### Crear carpeta de salida para la documentación HTML
```bash
mkdir -p docs/html
```

### Crear carpeta para PDF
```bash
mkdir -p docs/pdf
```

### Generar documentación JavaDoc de todo el código fuente
```bash
javadoc -d docs/html -sourcepath src -subpackages main.java
```

### Convertir cada HTML a PDF de forma individual
```bash
find docs/html -name "*.html" | while read file; do
  filename=$(basename "$file" .html)
  wkhtmltopdf --enable-local-file-access "$file" "docs/pdf/${filename}.pdf"
done
```

# b) Ejemplo de código documentado

[Enlace al archivo de ejemplo: HolaMundo.java](src/main/java/com/clases/HolaMundo.java)

### Fragmento del archivo de ejemplo
```java
/**
    * Suma dos números enteros.
    *
    * @param a Primer número
    * @param b Segundo número
    * @return Resultado de la suma de ambos números
    */
public int sumar(int a, int b) {
    return a + b;
}
```

# c) Formatos generados
- Documentación en formato HTML: [Enlace a la carpeta de documentación HTML](docs/html/)
- Documentación en formato PDF: [Enlace a la carpeta de documentación PDF](docs/pdf/)

# d) Explicación breve del workflow

Los pasos que da el workflow son los siguientes:
- Corre la última versión de Ubuntu.
```bash
build:
    runs-on: ubuntu-latest
```
- Se le da permisos de escritura a la MV.
```bash
permissions:
        contents: write
```
- Clona el repositorio en la MV.
```bash
- name: 📥 Clonar el repositorio
uses: actions/checkout@v4
```
- Instala y configura java en la MV.
```bash
- name: 🧰 Configurar Java
        uses: actions/setup-java@v4
        with:
          distribution: 'temurin'
          java-version: '17'
```
- Instala wkhtmltopdf en la MV.
```bash
- name: 🧰 Instalar wkhtmltopdf
        run: sudo apt-get update && sudo apt-get install -y wkhtmltopdf
```
- Genera la documentación HTML con Javadoc. (Preinstalado en Java)
```bash
- name: 🏗️ Generar documentación con Javadoc
        run: |
          mkdir -p docs/html
          javadoc -d docs/html -sourcepath src -subpackages main.java
```
- Genera la documentación PDF con wkhtmltopdf. (Conviertiendo todos los archivos HTML anteriores)
```bash
- name: 📄 Convertir HTML a PDF
        run: |
          mkdir -p docs/pdf
          find docs/html -name "*.html" | while read file; do
            filename=$(basename "$file" .html)
            wkhtmltopdf --enable-local-file-access "$file" "docs/pdf/${filename}.pdf"
          done
```
- Hace un push al repositorio con la nueva documentación.
```bash
- name: 💾 Subir los cambios generados a main
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add docs
          git commit -m "🔄 Actualización automática de documentación (Javadoc HTML y PDF)" || echo "Sin cambios que subir"
          git push origin master
```
- Por último, publica todo en Github Pages.
```bash
- name: 🚀 Publicar en GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./docs/html
```

# f) Evidencia de configuración SSH para Github

```bash
ssh -T git@github.com
The authenticity of host 'github.com (140.82.121.3)' can't be established.
ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'github.com' (ED25519) to the list of known hosts.
Hi nolocardeno! You've successfully authenticated, but GitHub does not provide shell access.
```

# g) Cómo clonar/usar el repositorio
Para poder clonar y usar este repositorio:
```bash
git clone https://github.com/nolocardeno/2526_DAW_u1_action1_1.git
```

# Cuestionario a responder
### a) ¿Qué herramienta o generador utilizaste en el workflow para crear la documentación en /docs?
En este caso he utilizado Javadoc para el HTML y wkhtmltopdf para convertir esos archivos HTML en PDFs.

### b) Muestra un fragmento del código con comentarios/docstrings estructurados que haya sido procesado por la herramienta. Comenta que estilo de documentación has utlizado.
El estilo de documentación utilizado es: Javadoc.

```bash
/**
    * Suma dos números enteros.
    *
    * @param a Primer número
    * @param b Segundo número
    * @return Resultado de la suma de ambos números
    */
public int sumar(int a, int b) {
    return a + b;
}
```

### c) ¿Qué segundo formato (además de HTML) generaste? Explica la configuración o comandos del workflow y herramientas que lo producen.
El segundo formato que he generado es PDF. Usando estos comandos en el workflow:
- Instala wkhtmltopdf en la MV.
```bash
- name: 🧰 Instalar wkhtmltopdf
        run: sudo apt-get update && sudo apt-get install -y wkhtmltopdf
```
- Genera la documentación PDF con wkhtmltopdf. (Conviertiendo todos los archivos HTML anteriores)
```bash
- name: 📄 Convertir HTML a PDF
        run: |
          mkdir -p docs/pdf
          find docs/html -name "*.html" | while read file; do
            filename=$(basename "$file" .html)
            wkhtmltopdf --enable-local-file-access "$file" "docs/pdf/${filename}.pdf"
          done
```

### d) Explica cómo GitHub facilita mantener la documentación cuando colaboran varias personas.
Github facilita mantener la documentación actualizada gracias Github Actions ya que con esta herramienta la generación de la misma no depende del usuario o desarrollador, si no que se genera automáticamente cada vez que subimos contenido al repositorio. A su vez, mediante el archivo Readme.md, como este mismo, todos los colaboradores del mismo repositorio o código pueden dejar comentarios, instrucciones o mensajes a los demás.

### e) Muestra mensajes de commit que evidencien el nuevo workflow. ¿Son claros y descriptivos? Justifícalo. Ademas de un conjunto de mensajes de tus commits.
- Automatización de documentación
- Arreglo de permisos de Pages
- Workflow actualizado

En este caso, no son todo lo claro y descriptivos que deberían, ya que es un repositorio creado especificamente para aprender el uso y configuración de Github actions y he tenido que hacer muchísimos commits, la mayoría de ellos para intentar arreglar fallos similares y los cuales ni siquiera tenía claros.
Entiendo que los commita deberian de ser mas descriptivos y deberian de explicar en pocas palabras la razón de ser del mismo.

### f) ¿Qué medidas/configuración del repositorio garantizan que solo personal autorizado accede al código y la documentación?
Para prevenir que cualquier pueda acceder al repositorio podemos poner el repositorio en Privado, lo cual limitara el acceso al mismo. Pudiendo establecer colaboradores manualmente si es esta la configuración del repositorio.
También puedes asignar roles en el repositorio a los colaboradores para que puedan realizar acciones concretas en el mismo dependiendo del rol asignado.

### g) Indica dónde en el README.md explicas el funcionamiento del workflow y dónde detallas las herramientas y comandos de documentación.
[Ir a Explicación breve del workflow](#d-explicación-breve-del-workflow)
[Ir a Herramientas usadas y comandos ejecutados](#a-herramientas-usadas-y-comandos-ejecutados)

### h) Justifica por qué el workflow utilizado es CI. ¿Qué evento dispara automáticamente la generación/actualización de la documentación?
El workflow utilizado es CI, ya que cada vez que hay cambios en el código fuente, este genera automáticamente la documentación de esos cambios, sin que lo haga nadie manualmente.
El evento que dispara automáticamente le generación de la documentación es el push al repositorio.

# Enlace a Github Pages
[Ir al sitio de Github Pages del repositorio](https://nolocardeno.github.io/2526_DAW_u1_action1_1/)