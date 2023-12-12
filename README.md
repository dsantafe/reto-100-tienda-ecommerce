# Despliegue de servicios (frontend y backends) en Docker del reto-100-tienda-ecommerce
Este repositorio contiene los archivos y comandos necesarios para desplegar los servicios (frontend y backends) en Docker del reto-100-tienda-ecommerce

1. Clona este repositorio:
```shell
git clone https://github.com/dsantafe/reto-100-tienda-ecommerce
cd reto-100-tienda-ecommerce
```

2. Construir la imagen:
```shell
$ docker build -t ms-frontend:1.0 frontend/.
$ docker build -t ms-products:1.0 products/.
$ docker build -t ms-merchandise:1.0 merchandise/.
$ docker build -t ms-shopping-cart:1.0 shopping-cart/.
$ docker images
```

3. Ejecutar los contenedores a partir de las imagenes:
```shell
$ docker run --name ms-frontend -d -p 3000:3000 \
-e PRODUCTS_SERVICE=host.docker.internal \
-e SHOPPING_CART_SERVICE=host.docker.internal \
-e MERCHANDISE_SERVICE=host.docker.internal \
ms-frontend:1.0

$ docker run --name ms-products -d -p 3001:3001 ms-products:1.0
$ docker run --name ms-shopping-cart -d -p 3002:3002 ms-shopping-cart:1.0
$ docker run --name ms-merchandise -d -p 3003:3003 ms-merchandise:1.0
```

Nota: También puedes ejecutar los contenedores a partir de un Docker Compose:
```shell
$ cd /deploy
$ docker-compose -p reto-100-tienda-ecommerce up -d
```

4. Desarrollar un pipeline de CI/CD en GitHub Actions que realice el build de la imagen y lo publique a Docker Hub.
- [Administre etiquetas y etiquetas con las acciones de GitHub](https://docs.docker.com/build/ci/github-actions/manage-tags-labels/)
```yaml
name: Frontend Build and Publish

on:
  workflow_dispatch:
  push:
    branches:
      - main    
    paths: 
      - 'frontend/*' 
    tags:
      - 'v*'

jobs:
  build-and-publish:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1

      - name: Login to Docker Hub
        uses: docker/login-action@v1
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Extract metadata (tags, labels) for Docker
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: dsantafe/ms-frontend
          flavor: latest=true
          tags: |
            type=semver,pattern={{version}}
            type=semver,pattern={{major}}.{{minor}}
            type=sha

      - name: Build and push Docker image
        uses: docker/build-push-action@v2
        with:
          context: ${{ github.workspace }}/frontend
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}

```

# Despliegue de Aplicación en Kubernetes usando Minikube y Argo CD
Este repositorio contiene los archivos y comandos necesarios para desplegar una aplicación en un clúster de Kubernetes utilizando Minikube y Argo CD.

## Pasos para desplegar la aplicación

1. Clona este repositorio:
```shell
git clone https://github.com/dsantafe/reto-100-tienda-ecommerce
cd reto-100-tienda-ecommerce
```

2. Iniciar Minikube:
Asegúrate de que Minikube esté iniciado antes de aplicar las configuraciones. Si no está iniciado, inicia Minikube con el siguiente comando:
```shell
$ minikube start
$ minikube dashboard
```

3. Instalar e iniciar Argo CD:
Instalación del Chart
```shell
$ kubectl create ns argo-cd
$ helm repo add argo https://argoproj.github.io/argo-helm
$ helm install argocd argo/argo-cd -n argo-cd
```

Para acceder a la interfaz de usuario del servidor, tiene las siguientes opciones y luego abra el navegador en http://localhost:8080 y acepte el certificado.
```shell
$ kubectl port-forward service/argocd-server -n argo-cd 8080:443
```

Después de acceder a la interfaz de usuario por primera vez, puede iniciar sesión con el nombre de usuario: admin y la contraseña aleatoria generada durante la instalación. Puede encontrar la contraseña ejecutando:
```shell
$ kubectl -n argo-cd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

4. Aplicar el archivo de ArgoCD:
Aplica el archivo de ArgoCD para desplegar la aplicación en Kubernetes. Este archivo referencia la carpeta /dev donde especifica el servicio para exponer la aplicación en un puerto específico en Minikube y cómo se deben ejecutar las réplicas de la aplicación.
```shell    
$ kubectl apply -f application.yaml
```

5. Obtener la URL del servicio:
Obtén la URL para acceder a la aplicación a través de Minikube.
```shell
$ kubectl port-forward service/ms-frontend -n reto-100-tienda-ecommerce-ns 3000:3000
```

6. Acceder a la aplicación http://localhost:3000

# Links
- Config repo: https://github.com/dsantafe/reto-100-tienda-ecommerce
- Docker repo: https://hub.docker.com/repository/docker/dsantafe
- Install ArgoCD: https://argo-cd.readthedocs.io/en/stable/getting_started/#1-install-argo-cd
- Login to ArgoCD: https://argo-cd.readthedocs.io/en/stable/getting_started/#4-login-using-the-cli
- ArgoCD Configuration: https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/
- Killercoda Interactive Environments: https://killercoda.com/