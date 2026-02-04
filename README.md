# Base de Conocimiento Personal

Esta es mi base de conocimiento personal, construida con:

- **Obsidian** para la creación y gestión de notas
- **MkDocs + Material Theme** para la publicación web
- **GitHub Pages** para hosting gratuito
- **GitHub Actions** para despliegue automático

## Estructura

- `docs/` - Contiene todas las notas en formato Markdown
- `.github/workflows/` - Automatización de despliegue
- `mkdocs.yml` - Configuración del sitio web

## Desarrollo local

```bash
# Instalar dependencias
pip install -r requirements.txt

# Servir localmente
mkdocs serve

# Construir sitio
mkdocs build

### **Paso 3: Configurar GitHub Actions**

```bash
# Crear directorio para workflows
mkdir -p .github/workflows

# Crear workflow de despliegue
cat > .github/workflows/deploy.yml << 'EOF'
name: Deploy MkDocs to GitHub Pages

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  workflow_dispatch:  # Permite ejecución manual

# Permisos necesarios para escribir en gh-pages
permissions:
  contents: write

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
      # 1. Descargar código
      - name: Checkout
        uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Para tener todo el historial
          
      # 2. Configurar Python
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.10'
          
      # 3. Instalar dependencias
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt
          
      # 4. Construir el sitio
      - name: Build site
        run: mkdocs build
          
      # 5. Subir artefacto (opcional, para debugging)
      - name: Upload artifact
        uses: actions/upload-artifact@v3
        with:
          name: site
          path: ./site
          
      # 6. Desplegar a GitHub Pages
      - name: Deploy
        if: github.ref == 'refs/heads/main'
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./site
          publish_branch: gh-pages
          force_orphan: true  # Mantiene solo los últimos archivos
          user_name: 'github-actions[bot]'
          user_email: 'github-actions[bot]@users.noreply.github.com'
EOF
