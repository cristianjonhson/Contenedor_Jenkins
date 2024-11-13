# Jenkins y Maven en Docker

Este proyecto configura un entorno de Jenkins y Maven en contenedores Docker para gestionar y ejecutar construcciones de proyectos Java. El proyecto incluye un Dockerfile para configurar Jenkins con OpenJDK 17 y Maven, y un archivo `docker-compose.yml` para la orquestación de contenedores.

## Estructura del Proyecto

- `app/Dockerfile`: Define el contenedor de Jenkins con Java 17 y Maven.
- `docker-compose.yml`: Orquesta los servicios `jenkins` y `maven-java`, compartiendo una red y un volumen para almacenar los datos de Jenkins.

## Contenido del Dockerfile

El Dockerfile en `app/Dockerfile` realiza las siguientes configuraciones:

1. **Imagen Base**: Usa `jenkins/jenkins:lts` como base.
2. **Usuario Root**: Cambia al usuario root para realizar instalaciones.
3. **Instalación de Java y Maven**:
   - Instala OpenJDK 17 y Maven para construir y ejecutar proyectos Java.
4. **Establece JAVA_HOME**: Configura la variable de entorno `JAVA_HOME` para Jenkins.
5. **Restablece Usuario Jenkins**: Vuelve al usuario Jenkins para ejecutar Jenkins en un entorno seguro.

## docker-compose.yml

El archivo `docker-compose.yml` orquesta los servicios:

### Servicios

- **Jenkins**:
  - Usa el Dockerfile desde `./app`.
  - Expone los puertos `8080` (interfaz de Jenkins) y `50000` (agente de Jenkins).
  - Monta el volumen `jenkins_home` en `/var/jenkins_home` para persistir los datos de Jenkins.
  - Se ejecuta en la red `my-network`.
  - Configura opciones de Java para Jenkins (`JAVA_OPTS`).

- **Maven-Java**:
  - Usa el mismo Dockerfile de Jenkins.
  - Configura `MAVEN_OPTS` para una mayor asignación de memoria (`-Xmx2g`).
  - Monta el directorio `./app` en `/app` para acceder al código fuente y permite la ejecución de Maven (`mvn clean install`).
  - Depende del servicio `jenkins` para garantizar que esté en ejecución antes de inicializar.
  - También se ejecuta en la red `my-network`.

### Redes y Volúmenes

- **Red**: Crea una red `my-network` para permitir la comunicación entre los contenedores.
- **Volumen**: Define `jenkins_home` para persistir los datos de Jenkins.

## Instrucciones para Ejecutar el Proyecto con Docker Compose

1. **Clona el Repositorio**:
   ```bash
   git clone <URL-del-repositorio>
   cd Contenedor_Jenkins
   ```

2. **Construye y Levanta los Contenedores**:
   Ejecuta el siguiente comando para construir y levantar ambos servicios:
   ```bash
   docker-compose up --build
   ```

3. **Accede a Jenkins**:
   Una vez que los contenedores estén en ejecución, abre tu navegador e ingresa a `http://localhost:8080` para acceder a Jenkins.
   - La configuración inicial de Jenkins requerirá un token, que puedes encontrar en los logs del contenedor de Jenkins:
     ```bash
     docker logs jenkins
     ```

4. **Ejecuta el Comando Maven**:
   El contenedor `maven-java` ejecutará el comando `mvn clean install` automáticamente para compilar y verificar el código en `./app`.

5. **Detener los Contenedores**:
   Para detener los servicios, ejecuta:
   ```bash
   docker-compose down
   ```

## Ejecución del Dockerfile Directamente

Si prefieres construir y ejecutar el contenedor de Jenkins manualmente usando el `Dockerfile` en lugar de `docker-compose`, sigue estos pasos:

1. **Construye la Imagen Docker desde el Dockerfile**:
   Navega al directorio donde se encuentra el Dockerfile y ejecuta:
   ```bash
   cd app
   docker build -t custom-jenkins .
   ```

2. **Ejecuta el Contenedor**:
   Ejecuta el contenedor creado a partir de la imagen `custom-jenkins` y mapea los puertos necesarios:
   ```bash
   docker run -d --name jenkins -p 8080:8080 -p 50000:50000 custom-jenkins
   ```

3. **Accede a Jenkins**:
   - Una vez que el contenedor esté en ejecución, abre tu navegador y accede a `http://localhost:8080` para entrar a la interfaz de Jenkins.
   - Para obtener la clave inicial de configuración, usa:
     ```bash
     docker logs jenkins
     ```

## Notas

- Asegúrate de tener Docker y Docker Compose instalados en tu sistema antes de ejecutar estos comandos.
- Personaliza el `docker-compose.yml` según tus necesidades, como añadir variables de entorno específicas o modificar los volúmenes compartidos.

## Contribuciones

Si encuentras problemas o tienes sugerencias para mejorar este proyecto, no dudes en abrir un _issue_ o enviar una _pull request_.