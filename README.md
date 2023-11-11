# Commands
## Build Docker
```bash
$ docker build -t ms-frontend:1.0 .
$ docker build -t ms-products:1.0 .
$ docker build -t ms-merchandise:1.0 .
$ docker build -t ms-shopping-cart:1.0 .
```

## Run Docker
```bash
$ docker run --name ms-frontend -d -p 3000:3000 \
-e PRODUCTS_SERVICE=host.docker.internal \
-e SHOPPING_CART_SERVICE=host.docker.internal \
-e MERCHANDISE_SERVICE=host.docker.internal \
ms-frontend:1.0

$ docker run --name ms-products -d -p 3001:3001 ms-products:1.0
$ docker run --name ms-shopping-cart -d -p 3002:3002 ms-shopping-cart:1.0
$ docker run --name ms-merchandise -d -p 3003:3003 ms-merchandise:1.0
```

## Run Docker Compose
```bash
$ docker-compose -p reto-100-tienda-ecommerce up -d
```

## Run Kubernetes Minikube
```bash
$ kubectl apply -f ms-frontend-deployment.yaml -n reto-100-tienda-ecommerce-ns
$ kubectl apply -f ms-products-deployment.yaml -n reto-100-tienda-ecommerce-ns
$ kubectl apply -f ms-shopping-cart-deployment.yaml -n reto-100-tienda-ecommerce-ns
$ kubectl apply -f ms-merchandise-deployment.yaml -n reto-100-tienda-ecommerce-ns
```

# Links
- Config repo:
- Killercoda Interactive Environments: https://killercoda.com/