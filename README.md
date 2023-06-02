# gha-backstage-docs

Esta accion de Github permite generar y subir la documentacion del proyecto en el que se usa esta accion al bucket S3 de donde se esta leyendo la documentacion desde el Backstage de Secuoyas.

Este proyecto [eats-its-own-dog-food](https://en.wikipedia.org/wiki/Eating_your_own_dog_food) y usa esta misma accion para enviar la documentacion al bucket de Backstage.

# Ejemplo:

El siguiente workflow ejecutara la generacion de la documentacion de nuestro repositorio y la subira a S3. Los unicos requisitos es que nuestro repositorio tenga los siguientes ficheros **en el directorio raiz** de nuestro repositorio:

- `catalog-info`.yaml: describe nuestro proyecto en Backstage

```yaml
apiVersion: backstage.io/v1alpha1
kind: Component
metadata:
  # deberia ser el nombre del repositorio
  name: nombre-del-repositorio
  # descripcion de lo que va el proyecto
  description: Mi super proyecto
  annotations:
    # url friendly dentro de Backstage
    github.com/project-slug: Secuoyas-Experience/nombre-del-repositorio
    # normalmente la documentacion empieza en el directorio raiz
    backstage.io/techdocs-ref: dir:.
  links:
    - url: https://github.com/Secuoyas-Experience/nombre-del-repositorio
      title: Titulo en Backstage
spec:
  # application | library | documentation
  type: library
  # infrastructure | secuoyas-dev | griddo-dev
  owner: infrastructure
  # griddo | toolbox | secuoyas
  system: toolbox
  # alpha | beta | production
  lifecycle: production
```

- `mkdocs.yml`: describe como esta estructurada nuestra documentacion

```yaml
site_name: 'nombre-del-repositorio'

# ficheros de documentacion que agregar (relativos al raiz)
nav:
  - index.md
  - install.md
  - releases.md
  - documentation.md

# plugins de mkdocs que queremos usar
plugins:
 - techdocs-core

# extensiones de markdown que queramos usar
markdown_extensions:
 - pymdownx.snippets
```

Una vez creados estos ficheros podemos agregar el siguiente pipeline a nuestro repositorio en `.github/workflows/techdocs.yml`:

```yaml
name: 'docs'
on:
  push:
    branches:
      - main
    paths:
      - "docs/**"
      - "mkdocs.yml"
      - ".github/workflows/techdocs.yml"

jobs:
  docs:
    runs-on: ubuntu-latest
    name: docs
    steps:
      - uses: actions/checkout@v3
      - uses: Secuoyas-Experience/gha-backstage-docs@v1
        with:
          AWS_BUCKET: ${{ secrets.AWS_BUCKET }}
          AWS_REGION: ${{ secrets.AWS_REGION }}
          AWS_ROLE_TO_ASSUME_ARN: ${{ secrets.AWS_ROLE_TO_ASSUME_ARN }}
```
