# gha-backstage-docs ![GitHub tag (latest by date)](https://img.shields.io/github/v/tag/Secuoyas-Experience/gha-backstage-docs)

Esta accion de Github permite generar y subir la documentacion del proyecto en el que se usa esta accion al bucket S3 de donde se esta leyendo la documentacion desde el Backstage de Secuoyas. Ademas este proyecto [eats-its-own-dog-food](https://en.wikipedia.org/wiki/Eating_your_own_dog_food) y usa esta misma accion para enviar la documentacion al bucket de Backstage.

# Inputs

- **aws_bucket**: bucket donde se subiran los ficheros
- **aws_region**: region de AWS
- **aws_role_to_assume_arn**: role de AWS con permisos para subir ficheros

# Ejemplo:

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
    # el bloque `permissions` es necesario para 
    # usar la autenticacion OIDC de AWS
    permissions:
      id-token: write
      contents: read    
    steps:
      - uses: actions/checkout@v3
      - uses: Secuoyas-Experience/gha-backstage-docs@v1
        with:
          aws_bucket: ${{ secrets.AWS_BUCKET }}
          aws_region: ${{ secrets.AWS_REGION }}
          aws_role_to_assume_arn: ${{ secrets.AWS_ROLE_TO_ASSUME_ARN }}
```

# Mas informacion

Puedes ver mas detalles [aqui](./docs/index.md)