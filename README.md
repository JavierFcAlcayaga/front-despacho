# Frontend Despacho - Innovatech Chile

## Descripción

Este repositorio contiene el frontend de la aplicación de despachos de Innovatech Chile. La aplicación fue desarrollada con React y Vite, y fue contenedorizada mediante Docker para ser desplegada en una instancia EC2 pública en AWS.

El frontend se comunica con los microservicios backend de Ventas y Despachos mediante un proxy Nginx, evitando exponer directamente los servicios backend a Internet.

## Tecnologías utilizadas

- React
- Vite
- Axios
- Tailwind CSS
- Docker
- Nginx
- AWS EC2
- AWS ECR

## Arquitectura de despliegue

El frontend se ejecuta en una instancia EC2 ubicada en la subred pública de la VPC. Esta instancia es el único componente accesible desde Internet.

La comunicación hacia los backend se realiza mediante proxy Nginx:

- `/api-ventas` → Backend Ventas privado `10.0.2.176:8080`
- `/api-despachos` → Backend Despachos privado `10.0.2.176:8081`

De esta forma, el navegador accede solo al Frontend público, mientras que los microservicios backend permanecen protegidos dentro de la subred privada.

## Variables de entorno

Archivo `.env`:

```env
VITE_API_VENTAS_URL=/api-ventas
VITE_API_DESPACHOS_URL=/api-despachos
```

## Dockerfile

El proyecto utiliza un Dockerfile multi-stage:

Primera etapa: construye la aplicación con Node.js.
Segunda etapa: sirve los archivos estáticos mediante Nginx.

Esto permite generar una imagen más liviana y optimizada para producción.

## Configuración Nginx

El archivo nginx.conf permite:

Servir la aplicación React.
Redirigir las peticiones del frontend hacia los backend privados.
Mantener la separación entre capa pública y capa privada.

## Ejecución local con Docker
docker build -t front-despacho .
docker run -d -p 80:80 --name front-despacho front-despacho
Acceso local:
http://localhost

## Despliegue en AWS EC2

La imagen fue publicada en Amazon ECR:
160661694072.dkr.ecr.us-east-1.amazonaws.com/front-despacho:latest
En la instancia EC2 pública se ejecuta mediante Docker Compose:
docker-compose up -d

## Evidencias de funcionamiento

Se validó:

Frontend accesible desde navegador mediante IP pública.
Contenedor frontend ejecutándose correctamente.
Proxy Nginx respondiendo hacia /api-ventas.
Proxy Nginx respondiendo hacia /api-despachos.
Comunicación correcta entre Frontend público y Backend privado.

## Seguridad

Solo la instancia Frontend posee acceso público por HTTP. Los backend no tienen IP pública y solo reciben tráfico interno desde la capa Frontend, respetando el principio de mínimo privilegio.
