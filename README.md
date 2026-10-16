<h2>TP 1<h2>

<h5>1.1<h5>
Using the -e flag allows you to change environment variables without rebuilding the Docker image, making the container more flexible and reusable across different environments. It also avoids hardcoding sensitive values directly in the Dockerfile.

<h5>1.2<h5>
We need a volume attached to our PostgreSQL container to persist database data even if the container is stopped, removed, or recreated. Without a volume, data stored inside the container can be lost when the container is deleted.

<h5>1.3<h5>

```
FROM postgres:17.2-alpine
COPY initdb/ /docker-entrypoint-initdb.d/
```

```
docker build -t tp-devops-database:1.0 ./database
```

```
docker network create app-network
docker volume create postgres-data
```

```
docker run -d --name database --network app-network -e POSTGRES_DB=db -e POSTGRES_USER=usr -e POSTGRES_PASSWORD=pwd -v postgres-data:/var/lib/postgresql/data tp-devops-database:1.0
```

<h5>1.4<h5>

A multistage build allows us to build the application in one stage and run it in another, producing a smaller, more efficient Docker image because Maven and the JDK are not included in the final image.

```FROM eclipse-temurin:21-jdk-alpine AS myapp-build```: Creates the build stage with Java 21 JDK.

```ENV MYAPP_HOME=/opt/myapp```: Defines an environment variable for the application directory.

```WORKDIR $MYAPP_HOME```: Sets the working directory.

```RUN apk add --no-cache maven```: Installs Maven to build the application.

```COPY pom.xml .```: Copies the Maven configuration file.

```COPY src ./src```: Copies the application source code.

```RUN mvn package -DskipTests```: Compiles the code and packages it into a JAR without running tests.

```FROM eclipse-temurin:21-jre-alpine```: Starts a new, lighter runtime stage with Java 21 JRE.

```ENV MYAPP_HOME=/opt/myapp and WORKDIR $MYAPP_HOME```: Set the application directory in the runtime stage.

```COPY --from=myapp-build $MYAPP_HOME/target/*.jar $MYAPP_HOME/myapp.jar```: Copies the generated JAR from the build stage.

```ENTRYPOINT ["java", "-jar", "myapp.jar"]```: Runs the Java application when the container starts.
<h5>1.5<h5>

A reverse proxy acts as an intermediary between clients and backend services. It receives incoming HTTP requests and forwards them to the appropriate service, such as our backend application. It simplifies access to the application, hides internal services, and can improve security.

<h5>1.6<h5>

Docker Compose lets us define and manage several related containers in one YAML file.
Without Compose, we must manually create containers, configure networks, attach volumes, set environment variables, and manage startup order.
With Compose, we can manage the entire application using a few commands

<h5>1.7<h5>

```docker compose up -d```	Creates and starts the services in the background.

```docker compose ps```	Displays the status of the services.

```docker compose logs```	Displays logs from all services.

```docker compose build```	Builds the service images.

```docker compose restart```	Restarts the services.

```docker compose stop```	Stops the services without removing their containers.

```docker compose pull```	Downloads the configured images from a registry.

<h5>1.8<h5>


<h5>1.9<h5>

```docker login```

then

```
docker tag tp-devops-database:1.0 guillaumebernardefrei/tp-devops-database:1.0
docker tag tp-devops-simple-api:1.0 guillaumebernardefrei/tp-devops-simple-api:1.0
docker tag tp-devops-httpd:1.0 guillaumebernardefrei/tp-devops-httpd:1.0

```
```
docker push guillaumebernardefrei/tp-devops-database:1.0
docker push guillaumebernardefrei/tp-devops-simple-api:1.0
docker push guillaumebernardefrei/tp-devops-httpd:1.0
```

<h5>1.10<h5>

We publish Docker images to an online repository such as Docker Hub so that they can be shared and downloaded by other developers, team members, and deployment servers.

<h2>TP 2<h2>

<h5>2.1<h5>
Testcontainers is a Java library that uses Docker containers to run automated tests with real dependencies, such as PostgreSQL databases. It automatically starts the required containers before tests and stops them afterward, making tests more realistic, isolated, and reproducible.

<h5>2.2<h5>
GitHub secured variables (Secrets) are used to store sensitive information, such as API keys, passwords, and Docker Hub credentials, without exposing them in the source code. In GitHub Actions, they allow workflows to securely access these values during tasks such as logging in to Docker Hub and publishing Docker images.

<h5>2.3<h5>
We use needs: test-backend to ensure that the Docker image is built and published only after the backend tests have passed. Without it, GitHub Actions could build and publish an image even if the tests fail, potentially deploying faulty code.

<h5>2.4<h5>

We push Docker images to Docker Hub so they can be stored online and downloaded or deployed on other machines without rebuilding them from the source code. This makes it easier to share and deploy our application containers across different environments.

<h2>TP 3<h2>


