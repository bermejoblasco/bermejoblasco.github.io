---
title: Creando un cluster de AKS con Terraform
post_date: 2020-05-10 00:00:00
author: rbermejo
layout: post
---

# El Origen

Cuando empiezas un proyecto una de las cosas que siempre debes crear es la infraestructura.  
Una buena práctica es tener tu **Infraestructura como código**, <!--break-->de esta forma podéis tener vuestra infraestructura versionada y automatizada.

## Terraform  
[Terraform](https://www.terraform.io/intro/index.html) es una herramienta para construir, cambiar y versionar tú infraestructura de forma segura y eficpiente.  
Hay otras herramientas como [Pulimi](https://www.pulumi.com/) o [ARM](https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/overview) entre otras, pero particularmente me gusta **Terraform** por su nomenclatura y la forma de escribirla.

## Estructura  
La estructura del proyecto es muy simple, dado que vamos a desplegar un cluster básico sin auto escalado o networking por ejemplo.  
Por eso solo vamos a necesitar tres archivos:  
* main.tf  
* output.tf
* variables.tf  

Estos tres archivos son los tres archivos base de un Terraform.

### Main.tf  
Este es el archivo principal de Terraform, el archivo de entrada cuando ejecutamos Terraform.  
En nuestro caso este archivo tendrá el siguiente aspecto:  
```  
provider "azurerm" {
    version = "~>2.0"
    subscription_id     = "${var.azure_subscription_id}"
    tenant_id           = "${var.azure_tenenat_tenant_id}"
    features {}
}

resource "azurerm_resource_group" "example" {
  name     = "rg-${var.prefix}-k8s-resources"
  location = "${var.location}"
}

resource "azurerm_kubernetes_cluster" "example" {
  name                = "${var.prefix}-k8s"
  location            = "${azurerm_resource_group.example.location}"
  resource_group_name = "${azurerm_resource_group.example.name}"
  dns_prefix          = "${var.prefix}-k8s"

  linux_profile {
    admin_username = "ubuntu"

    ssh_key {
            key_data = file(var.ssh_public_key)
        }
  }

    service_principal {
    client_id     = "${var.kubernetes_client_id}"
    client_secret = "${var.kubernetes_client_secret}"
  }

   default_node_pool {
    name       = "default"
    node_count = 1
    vm_size    = "Standard_D2_v2"
  }

  tags = {
    Environment = "Production"
  }
}
```  
De este código hay destacar varios puntos:  
1. Necesitamos tener un service principal con permisos para poder crear y acceder un cluster de AKS.  
2. En este caso hemos creado un nodo con nombre "default" cuya VM que hay por detrás de la familia Standard_D2_v2, aquí podríamos definir varios nodos otro tipo de máquinas....
3. Debemos crear una ssh_key para poder acceder luego a los nodos si así lo deseamos.  

En este [link](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#default_node_pool) podéis ver todos los campos y apartados que podéis usar.

### Variables.tf  
Archivo donde definiremos los valores qeu las variables usarán dentro de nuestro main.tf, en este caso sería así:  

```  
variable "prefix" {
  description = "A prefix used for all resources in this example"
  default = "TestRob"
}

variable "location" {
  description = "The Azure Region in which all resources in this example should be provisioned"
  default = "West Europe"
}

variable "ssh_public_key" {
    default = "ssh.pub"
}

variable "kubernetes_client_id" {
  description = "The Client ID for the Service Principal to use for this Managed Kubernetes Cluster"
  default = "xxx-2526-4422-90db-xxx"
}

variable "kubernetes_client_secret" {
  description = "The Client Secret for the Service Principal to use for this Managed Kubernetes Cluster"
  default = "54c46dfc-4da3-4f56-bf0b-xxxxxx"
}

variable "azure_subscription_id" {
  description = "The azure subscription"
  default = "xxxx-9f89-40aa-a717-xxxx"
}

variable "azure_tenenat_tenant_id" {
  description = "The azure Tenant"
  default = "xxxx-8106-48a2-a786-xxxx"
}
```  

***¿Como creamos una clave SSH?***  
Para crear una key ssh en window 10, se debe lanzar el siguiente command y seguir las instrucciones:  
```
ssh-keygen -t rsa -b 4096
```  
El resultado es el siguiente:  
![Create SSH Key in Windows 10](/public/images/2020/05/ssh.PNG)  
El resultado son dos ficheros, el que en este caso nos interesa es el que tiene la extensión .pub que es el que añadimos en las variables.  

### Output.tf  
Este es el último archivo que necesitamos y donde definiremos aquellas variables que queremos que Terraform devuelva, en este ejemplo:  
```  
output "id" {
  value = "${azurerm_kubernetes_cluster.example.id}"
}

output "kube_config" {
  value = "${azurerm_kubernetes_cluster.example.kube_config_raw}"
}

output "client_key" {
  value = "${azurerm_kubernetes_cluster.example.kube_config.0.client_key}"
}

output "client_certificate" {
  value = "${azurerm_kubernetes_cluster.example.kube_config.0.client_certificate}"
}

output "cluster_ca_certificate" {
  value = "${azurerm_kubernetes_cluster.example.kube_config.0.cluster_ca_certificate}"
}

output "host" {
  value = "${azurerm_kubernetes_cluster.example.kube_config.0.host}"
}
```  
Ahora lo único que nos queda es lanzar el terrafrom para ello necesitamos lanzar tres comandos:  
```  
terraform init 
terraform validate
terraform plan
terraform apply
```  
Ahora ya tendríamos creado nuestro cluster y si vamos al portar veríamos nuestro AKS creado:  
![AKS creado en el portal](/public/images/2020/05/kubernetes.PNG)  

Ahora con kubectl podemos conectarnos a nuestro cluster y ver el nodo creado:  
``` 
 kubectl get nodes
```  
![AKS kubectl get nodes](/public/images/2020/05/kubectlaks.PNG) 
 
# Conclusión  
Debemos exigirnos tener nuestra infraestructura como código, ya no solo para poder desplegarla fácilmente o modificarla, sino también para poder tenerla versionada como si fuera código.  
Por otro lado se puede ver que crear un cluster de AKS con Terraform, uno muy básico, es baastante sencillo y hacerlo con Terraform concretamente nos facilita mucho la vida.

Espero os pueda ayudar.