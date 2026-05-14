**=== Sistema de Despliegue Contenerizado en AWS EC2 ===**
**Descripción del Proyecto:**

Este proyecto se centra en  la contenedorización, automatización de despliegue y ejecución de una solución compuesta por Frontend y Backend para la empresa Innovatech Chile.
La solución implementa una arquitectura basada en microservicios desplegada sobre infraestructura AWS utilizando Amazon EC2, Docker, Docker Compose, GitHub Actions y Docker Hub.

**El sistema está compuesto por:**
- Frontend desarrollado en React + Vite
- Backend de Despacho desarrollado en Spring Boot
- Backend de Ventas desarrollado en Spring Boot
- Base de datos MySQL para Despcho
- Base de datos MySQL para Ventas
- 
El objetivo principal fue automatizar completamente el proceso de construcción, publicación y despliegue de contenedores mediante un pipeline CI/CD activado desde GitHub Actions sobre la rama deploy.

**Tecnologías Utilizadas:**

**Frontend:**
- React 18
- Vite
- JavaScript (JSX)
- Nginx

**Backend:**
- Java 17
- Spring Boot 3
- Maven
- Spring Data JPA
- Swagger OpenAPI
- Infraestructura y DevOps
- Docker
- Docker Compose
- GitHub Actions
- Docker Hub
- AWS EC2
- AWS Systems Manager (SSM)
- MySQL 8

**Arquitectura General:**
La solución fue desplegada utilizando dos instancias EC2 separadas.

**EC2 Pública:**
- Frontend React + Vite servido con Nginx
- acceso público mediante Elastic IP
**Responsabilidad:**
Permitir acceso desde navegador externo.

**EC2 Privada:**
- Backend de Despacho
- Backend de Ventas
- MySQL Despcho
- MySQL Ventas
**Responsabilidad:**
Procesamiento de lógica de negocio y persistencia de datos sin exposición directa a Internet.
Esto cumple con el principio de seguridad donde únicamente el frontend es accesible públicamente.

**Contenedorización del Frontend:**
El frontend fue desarrollado con React + Vite y desplegado mediante Nginx dentro de un contenedor Docker.

**Dockerfile Frontend:**
Se implementó Dockerfile multi-stage utilizando:
node:18-alpine
lo que permite instalación de dependencias y a su vez compilación de producción mediante npm run build.

**Etapa de Producción:**
nginx:alpine
lo que nos permitio el poder servir archivos estáticos optimizados y a su vez un reducción del tamaño final de la imagen.

**Buenas prácticas aplicadas**
- multi-stage build
- uso de usuario no root
- optimización de capas
- permisos controlados
- menor superficie de ataque

**Docker Compose Frontend**
Permite levantar automáticamente el servicio frontend.

**Configuración principal**
- imagen desde Docker Hub
- puerto expuesto 80
- volumen para logs de Nginx
- reinicio automático


**Contenedorización del Backend**
El backend está compuesto por dos microservicios:
- despacho-backend
- ventas-backend
ambos desarrollados con Spring Boot y conectados a sus respectivas bases de datos MySQL.

**Dockerfile Backend**
Se implementó Dockerfile multi-stage utilizando:
**Etapa de Build**
maven:3.9-eclipse-temurin-17

Permite:
- descarga de dependencias
- compilación del proyecto
- generación del archivo JAR

**Buenas prácticas aplicadas**
- multi-stage build
- usuario no root
- optimización de imagen
- separación build/runtime
- seguridad reforzada

**Docker Compose Backend**
- 2 microservicios Spring Boot
- 2 bases de datos MySQL
- dependencias entre servicios
- healthchecks
- reinicio automático
- persistencia mediante volúmenes

**Servicios**
**despacho-backend**
- puerto 8081
- base de datos despacho_db
**ventas-backend**
- puerto 8080
- base de datos ventas_db

**Persistencia de Datos**
Se implementó persistencia mediante named volumes.

**Volúmenes utilizados**
- despacho-db-data
- entas-db-data

**Justificación**
Se eligió named volume porque:
- evita pérdida de información
- mantiene integridad de datos
- facilita respaldo
- mejora continuidad operativa
- desacopla almacenamiento del host
- Esto cumple con los requerimientos de persistencia exigidos.

**Configuración de Base de Datos**
Se utiliza MySQL 8 para ambos microservicios.

**Conexión interna**
La comunicación se realiza mediante red interna de Docker utilizando nombres de servicio y sin exposición pública.

**Infraestructura AWS**
**Frontend**
- EC2 pública
- subred pública
- Elastic IP asignada
**Acceso:http://100.51.106.65/**

**Backend**
- EC2 privada
- subred privada
- salida mediante NAT Gateway
  
Esto garantiza seguridad y separación adecuada entre capas.

**Security Groups**
**Frontend EC2**
Puertos permitidos:
22/SSH
80/HTTP
ICMP/conectividad

**Backend EC2**
Puertos internos:
8080/ventas-backend
8081/despacho-backend
3306/MySQL
Estos no están expuestos a Internet y solo permiten acceso interno autorizado.

**Pipeline CI/CD**
Se implementó automatización mediante GitHub Actions utilizando trigger sobre la rama deploy.

**Flujo del Pipeline**
Push a rama deploy
Cada actualización sobre deploy inicia automáticamente el pipeline.

**Build de imagen Docker**
Se construye una nueva imagen Docker desde el Dockerfile correspondiente.

**Publicación en Docker Hub**
repositorios:
dockermasterluka/despacho-frontend
dockermasterluka/despachos-backend
dockermasterluka/ventas-backend

**Despliegue automático en EC2**
Mediante AWS Systems Manager (SSM) se ejecutan comandos remotos:
- docker pull
- docker stop
- docker rm
- docker-compose up -d
Esto permite despliegue continuo sin intervención manual.

**Gestión de Secrets**
**Secrets utilizados**
- DOCKERHUB_USERNAME
- DOCKERHUB_TOKEN
- AWS_ACCESS_KEY_ID
- AWS_SECRET_ACCESS_KEY
- AWS_SESSION_TOKEN
- AWS_REGION
- EC2_FRONTEND_INSTANCE_ID
- EC2_INSTANCE_ID

**Swagger**
Los microservicios backend exponen documentación mediante:
**/swagger-ui.html**

**Integración Frontend -> Backend**
El frontend desplegado en la instancia pública consume los endpoints del backend ubicado en la instancia privada.
Permitiendo así:
- visualización de datos
- operaciones CRUD
- separación segura entre capas
- cumplimiento de arquitectura Front -> Back
La comunicación respeta las políticas de Security Groups definidas.

**Ejecución Local**

**Frontend**
docker build -t despacho-frontend .
docker run -p 80:80 despacho-frontend

**Backend**
docker-compose -f docker-compose.backend.yml up -d

**Verificar contenedores**
docker ps

**Principios DevOps Aplicados**
Durante el desarrollo se aplicaron:

- contenedorización con Docker
- automatización CI/CD
- persistencia mediante volúmenes
- control de versiones con Git
- despliegue automatizado en AWS
- gestión de entornos
- trazabilidad mediante logs
- microservicios desacoplados
- separación de infraestructura pública y privada
Estas prácticas mejoran escalabilidad, mantenibilidad y seguridad.

**Con esto podemos concluir**
Que la solución implementada permite ejecutar el sistema de forma segura, automatizada y mantenible dentro de AWS.
El uso de Docker, GitHub Actions, Docker Hub, EC2 y despliegue automatizado reduce errores manuales, mejora la velocidad de entrega y prepara la infraestructura para futuras escalas dentro de Innovatech Chile.
El proyecto cumple con los requerimientos funcionales y técnicos exigidos para la evaluación, incluyendo contenedorización, persistencia, automatización CI/CD y despliegue controlado sobre AWS.
