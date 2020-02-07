# DockerizandoAppJava

En este ejemplo dockerizamos una app de java usando como base de datos postgresql

## Para crear un contenedor de postgres

```bash
docker run --name postgresqlAppJava -p 5432:5432 -e POSTGRES_PASSWORD=postgresql -d postgres
```

## Obtener más información acerca de la base de datos postgres

```bash
docker exec -ti postgresqlAppJava psql -U postgres -W postgres
```

## Añadimos las dependencias al pom.xml de nuestro proyecto

```bash
spring.datasource.url=jdbc:postgresql://localhost:5432/postgres
spring.datasource.username=postgres
spring.datasource.password=postgresql
spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=create-drop
spring.datasource.initialization-mode=always
```

## Creamos un dockerfile para generar nuestra imagen

```bash
FROM openjdk:8-jdk-alpine
VOLUME /tmp
COPY "./target/cemapp-0.0.1-SNAPSHOT.jar" "app.jar"
EXPOSE 9000
ENTRYPOINT ["java","-jar","app.jar"]
```

## Construimos la imagen

```bash
docker build -t appJavaPostgres .
```

## Creamos el contenedor con nuestra imagen

```bash
docker run --name apirestJavaPostgres -p 9001:9000 -d appjavapostgres/apirest
```

## Si deseamos hacerlo a través de docker-compose

```bash
version: '2'
services:
  app:
    build: .
    image: appjavapostgres/apirest
    ports:
      - "9001:9000"
    depends_on:
        - mypostgres
        
  mypostgres:
    image: postgres
    ports:
      - "5432:5432"
    environment:
      - POSTGRES_PASSWORD=postgresql
      - POSTGRES_USER=postgres
      - POSTGRES_DB=postgres
```

Cambiamos en el pom el 'localhost' por 'mypostgres' spring.datasource.url=jdbc:postgresql://mypostgres:5432/postgres

## Levantamos nuestro docker-compose

```bash
docker-compose up -d
```
