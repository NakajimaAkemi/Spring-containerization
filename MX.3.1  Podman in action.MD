# Overview
In this section we will do advanced applications of Podman, such as Pods networking, Podman kube commands and more.


## Pods networking
Podman uses bridges networks in order to let containers to communicate with eachother, when a pod is created, Podman automatically creates automatically a bridge network.
For the communication between pods, we need to configure a network in order to allow them to communicate.

1. Network setup
```bash
podman network create mynetwork
```

2. Pod creation and linkage to our network
```bash
podman pod create --name pod1 --network mynetwork
podman pod create --name pod2 --network mynetwork
```

3. Container execution inside a Pod
```bash
podman run -dt --pod pod1 --name app-container your-app-name
podman run -dt --pod pod2 --name db-container postgres:13
```

Podman compose configuration

```yaml
version: '3.8'
services:
  app:
    image: your-app-name
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "8080:8080"
    networks:
      - mynetwork

  db:
    image: postgres:13
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: database
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - mynetwork

volumes:
  postgres_data:

networks:
  mynetwork:
    driver: bridge

```

## Podman kube
YAML file example

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo-app-deployment
  labels:
    app: demo-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: demo-app
  template:
    metadata:
      labels:
        app: demo-app
    spec:
      containers:
        - name: demo-app
          image: demo-app:latest
          ports:
            - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: demo-app-service
spec:
  selector:
    app: demo-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: LoadBalancer
```
## Communication inside the Pod

## SELinux and Apparmor

## Logging and Monitoring


## CI/CD Pipeline
