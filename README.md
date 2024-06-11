# Backend-Java-SprintBoot

## PC02-Guia del SprintBoot Configuration 
 
Presione el link para generar el proyecto en sprintBoot: [Generador de Proyectos Spring Boot](https://start.spring.io/#!type=maven-project&language=java&platformVersion=3.2.6&packaging=jar&jvmVersion=21&groupId=com.%3CNombreDeLaPc%3E.platform&artifactId=u%3Cc%C3%B3digo-estudiante%3E&name=u%3Cc%C3%B3digo-estudiante%3E&description=PC2%20Open%20Source&packageName=com.%3CNombreDeLaPc%3E.platform.u%3Cc%C3%B3digo-estudiante%3E&dependencies=data-jpa,validation,web,modulith,devtools,postgresql,lombok)

## Base de datos

Abrir pgAdmin y crear la base de datos: `<Nombre que requiere la Pc>`.

## application.properties

Abrir el archivo `application.properties` y agregar el siguiente código:
```
server.port: <server que te pide >

### PostgreSQL
spring.datasource.driver-class-name: org.postgresql.Driver
###    JDBC : SGDB :// HOST : PORT / DB
spring.datasource.url: jdbc:postgresql://localhost:5432/<Nombre que requiere la Pc>
spring.datasource.username: postgres
spring.datasource.password: 1234

spring.jpa.database: postgresql
spring.jpa.hibernate.ddl-auto: update
spring.jpa.show-sql: true

spring.jpa.properties.hibernate.format_sql: true
spring.jpa.properties.hibernate.dialect: org.hibernate.dialect.PostgreSQLDialect
```

## Project structur for shared

En el package principal `pe.edu.upc.learning.platform`, crear la siguiente estructura para el package `shared`.

```markdown
- 📁 shared
  - 📁 domain.model.entities
    - 📄 AuditableModel.java
  - 📁 infrastructure
    - 📁 documentation.openapi.configuration
    - 📁 persistence.jpa.strategy
  - 📁 interfaces.rest.resources
    - 📄 MessageResource.java
```

En el package `domain.model.entities` crear la clase `AuditableModel` con el siguiente contenido:

```java
import jakarta.persistence.Column;
import jakarta.persistence.EntityListeners;
import jakarta.persistence.MappedSuperclass;
import lombok.Data;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import java.util.Date;

@EntityListeners(AuditingEntityListener.class)
@MappedSuperclass
@Data
public class AuditableModel {

    @CreatedDate
    @Column(nullable = false, updatable = false)
    private Date createdAt;

    @LastModifiedDate
    @Column(nullable = false)
    private Date updatedAt;
}
```

En el package `interfaces.rest.resources` crear el record `MessageResource` con el siguiente contenido:
```java
public record MessageResource(String message) {
}
```


# Swagger UI

Usa la url  http://localhost:8090/swagger-ui/index.html para ejecutar el API.
