
# my first k8s application for Ktrust

this is a small project that serve 1 HTML page, technologies that used: kubernetes, minikube, Docker, terraform.

## minikube

1) ### clone the repo 
``` 
git clone https://github.com/Benzeltzer/first-k8s-app
cd /Desktop/project Ktrust
```

2) ### Build the image
```
docker build -t mynginx ./
```
3) ### setting minikube
using the tutorial : https://minikube.sigs.k8s.io/docs/start/.


4) ### Start minikube
```
minikube start
```
5) ### Push mynginx into minikube docker registry using this tutorial:
 https://minikube.sigs.k8s.io/docs/handbook/registry/

6) ### create terraform configuration
``` 
provider "kubernetes" {
  config_path = "~/.kube/config"  # Adjust the path to your kubeconfig file
}

resource "kubernetes_deployment" "nginx" {
  metadata {
    name = "nginx-deployment"
    labels = {
      app = "nginx"
    }
  }

  spec {
    replicas = 3

    selector {
      match_labels = {
        app = "nginx"
      }
    }

    template {
      metadata {
        labels = {
          app = "nginx"
        }
      }

      spec {
        container {
          image = "mynginx:1.0"
          name  = "nginx-container"
          ports {
            container_port = 80
          }
        }
      }
    }
  }
}

resource "kubernetes_service" "nginx" {
  metadata {
    name = "nginx-service"
  }

  spec {
    selector = {
      app = kubernetes_deployment.nginx.metadata[0].labels.app
    }

    port {
      port        = 80
      target_port = 80
    }

    type = "NodePort"
  }
}
```
7) ### run terraform 
```
terraform init
terraform apply
```
8) ### Find HTTP and HTTPS IP adresses

```
minikube service list
```
you should see : 
```
----------------------|---------------------------|--------------|---------------------------|
|      NAMESPACE       |           NAME            | TARGET PORT  |            URL            |
|----------------------|---------------------------|--------------|---------------------------|
| default              | kubernetes                | No node port |                           |
| default              | nginx-service             |           80 | http://192.168.49.2:32718 |
| kube-system          | kube-dns                  | No node port |                           |
| kubernetes-dashboard | dashboard-metrics-scraper | No node port |                           |
| kubernetes-dashboard | kubernetes-dashboard      | No node port |                           |
|----------------------|---------------------------|--------------|---------------------------|

```
for this example you can open your browser and check the following address:

### HTTP : 

http://192.168.49.2:30001/



