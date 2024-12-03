# Guía para trabajar con Docker y Azure Container Instances
## Prerequisito: Generar imagen de Docker

### Click derecho en tu proyecto en Visual Studio > Add > Docker Support
![alt text](https://raw.githubusercontent.com/eduardoac96/AZ204/main/images/image-2.png)

![alt text](https://raw.githubusercontent.com/eduardoac96/AZ204/main/images/image-7.png)
### Dockerfile generado
![alt text](https://raw.githubusercontent.com/eduardoac96/AZ204/main/images/image-16.png)

## Paso 1: Construir la Imagen de Docker

```docker build -t miproyecto:v1 .```

![alt text](https://raw.githubusercontent.com/eduardoac96/AZ204/main/images/image-3.png)

-t es para determinar el nombre de la imagen docker seguido de un tag, en este ejemplo es v1
 .  Determina el Directorio actual

### Listar imagenes de docker

```docker images```

![alt text](https://raw.githubusercontent.com/eduardoac96/AZ204/main/images/image-12.png)

### Ejecutar/crear un contenedor
```docker run -d -p 5050:8080 miproyecto:v1```

![alt text](https://raw.githubusercontent.com/eduardoac96/AZ204/main/images/image-18.png)

-d: Detached, establece que el contenedor se ejecutara en segundo plano

-p: Port Mapping, determina el puerto de la maquina que ejecuta el contenedor (en este caso 5050 con uno de los puertos del cual el contenedor esta escuchando (8080)


## Paso 2: Subir Docker Image a Azure Container Registry

### 1. Iniciar sesion con Azure CLI 
```az login```

### Seleccionar tu subscripcion de Azure
```az account set --subscription 805bbde1-1929-4e72-8f9f-8fdd0d839b6d```

### Crear un grupo de recursos
```az group create --name rg-accenture-east-us-96 --location eastus```


### 2. Crear un recurso de Azure Container Registry:
```    az acr create --resource-group rg-accenture-east-us-96 --name miproyectoacr --sku Basic```

### 3. Iniciar sesión en ACR:

   ```az acr login --name miproyectoacr```

```docker tag miproyecto:v1 miproyectoacr.azurecr.io/miproyecto:v1```



### 4. Compilar un Docker file y push a ACR:
```docker push miproyectoacr.azurecr.io/miproyecto:v1```

![alt text](https://raw.githubusercontent.com/eduardoac96/AZ204/main/images/image-19.png)
### 5. Ver las imágenes en ACR:
```az acr repository list --name miproyectoacr```

![alt text](https://raw.githubusercontent.com/eduardoac96/AZ204/main/images/image-22.png)

## Paso 3. Azure Container Instances

####  Para exponer tu contenedor a Internet, es necesario asignarle un nombre DNS único. A continuación, se muestra cómo crear y asignar un nombre DNS utilizando Azure Cloud Shell:
 
```az container create --resource-group rg-accenture-east-us-96 --name mi-app-contenedor-5000 --image miproyectoacr.azurecr.io/miproyecto:v3 --ip-address public --dns-name-label acn-demo24 --ports 8080 --cpu 1 --memory 1 ```

### 3.1 Por efectos de prueba, puedes intentar con una imagen en un registry publico
```az container create --resource-group rg-accenture-east-us-96 --name mi-app-contenedor-6000 --image mcr.microsoft.com/k8se/quickstart:latest --ip-address public --dns-name-label acn-demo24 --ports 80 --cpu 1 --memory 1 ```
 
```az container show --resource-group rg-accenture-east-us-96 --name mi-app-contenedor-6000 --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table```
 ![alt text](https://raw.githubusercontent.com/eduardoac96/AZ204/main/images/image-28.png)![alt text](https://raw.githubusercontent.com/eduardoac96/AZ204/main/images/image-27.png)
## Paso 4. Eliminar recursos de Azure
#### Por efectos de prueba y no incurrir en costos extras de Azure, procedemos a eliminar el recurso de Container Instances (o detenerlo si lo deseas).


```az container delete --resource-group rg-accenture-east-us-96 --name mi-app-contenedor-6000```

```az container list --resource-group rg-accenture-east-us-96 --output table```

```az group delete --name rg-accenture-east-us-96```


| Parametro| Descripcion |
| -------- | -------- | 
| --resource-group | El grupo de recursos en el que se creará la instancia de contenedor|
| --name  | El nombre que se asignará al contenedor.|
| --image  | La imagen de Docker que se utilizará. |
| --ports  |Especifica los puertos que estarán expuestos. En este ejemplo, el puerto 80.|
| --dns-name-label  | El nombre DNS único que se generó en el paso anterior.|
| --location | La región en la que se creará el contenedor|