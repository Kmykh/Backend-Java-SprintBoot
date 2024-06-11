# Backend-Java-SprintBoot

## PC02-Guia del SprintBoot Configuration 
 
Presione el link para generar el proyecto en sprintBoot: [Generador de Proyectos Spring Boot](https://start.spring.io/#!type=maven-project&language=java&platformVersion=3.2.6&packaging=jar&jvmVersion=21&groupId=com.%3CNombreDeLaPc%3E.platform&artifactId=u%3Cc%C3%B3digo-estudiante%3E&name=u%3Cc%C3%B3digo-estudiante%3E&description=PC2%20Open%20Source&packageName=com.%3CNombreDeLaPc%3E.platform.u%3Cc%C3%B3digo-estudiante%3E&dependencies=data-jpa,validation,web,modulith,devtools,postgresql,lombok)

## Base de datos

Abrir pgAdmin y crear la base de datos: `<Nombre que requiere la Pc>`.

## En el pom.xml 

Abrir el .xml y agregar el siguiente codigo en dependecy:

```
		<!-- https://mvnrepository.com/artifact/io.github.encryptorcode/pluralize -->
		<dependency>
			<groupId>io.github.encryptorcode</groupId>
			<artifactId>pluralize</artifactId>
			<version>1.0.0</version>
		</dependency>
		<!-- https://mvnrepository.com/artifact/org.springdoc/springdoc-openapi-starter-webmvc-ui -->
		<dependency>
			<groupId>org.springdoc</groupId>
			<artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
			<version>2.5.0</version>
		</dependency>
		<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-security -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-security</artifactId>
		</dependency>
  
  ```

## application.properties

Abrir el archivo `application.properties` y agregar el siguiente código:
```
spring.application.name=<sucodigo>
server.port: <ponga el puerto que le pide>

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
sping.jpa.hibernate.naming.physical-strategy:com.opentable.platform.<sucodigo>.shared.infrastructure.persistence.jpa.configuration.strategy
```

## Lo que va en el shared

En el package principal `pe.edu.upc.learning.platform`, crear la siguiente estructura para el package `shared`.

```markdown
- 📁 shared
  - 📁 domain.model
    - 📁 aggregates
     - 📄 AuditableAbstractAggregateRoot.java
    - 📁 entities
     - 📄 AuditableModel.java
  - 📁 infrastructure
    - 📁 documentation.openapi.configuration
      - 📄 OpenApiConfiguration.java
    - 📁 persistence.jpa.strategy
      - 📄 SnakeCaseWithPluralizedTablePhysicalNamingStrategy.java
  - 📁 interfaces.rest.resources
    - 📄 MessageResource.java
```

En el package `domain.model.aggregates` crear la clase `AuditableAbstractAggregateRoot` con el siguiente contenido:

```java
import jakarta.persistence.*;
import lombok.Getter;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.domain.AbstractAggregateRoot;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import java.util.Date;

@EntityListeners(AuditingEntityListener.class)
@MappedSuperclass
public class AuditableAbstractAggregateRoot<T extends AbstractAggregateRoot<T>> extends AbstractAggregateRoot<T> {

  @Id
  @Getter
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  private Long id;

  @Getter
  @CreatedDate
  @Column(nullable = false, updatable = false)
  private Date createdAt;

  @Getter
  @LastModifiedDate
  @Column(nullable = false)
  private Date updatedAt;
}
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

En el package `infrastructure.documentation.openapi.configuration` crear la clase `OpenApiConfiguration` con el siguiente contenido:

```java
import io.swagger.v3.oas.models.Components;
import io.swagger.v3.oas.models.ExternalDocumentation;
import io.swagger.v3.oas.models.OpenAPI;
import io.swagger.v3.oas.models.info.Info;
import io.swagger.v3.oas.models.info.License;
import io.swagger.v3.oas.models.security.SecurityRequirement;
import io.swagger.v3.oas.models.security.SecurityScheme;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class OpenApiConfiguration {
  @Bean
  public OpenAPI learningPlatformOpenApi() {
    // General configuration
    var openApi = new OpenAPI();
    openApi
        .info(new Info()
            .title("<Nombre de tu pc >")
            .description("<Nombre de tu pc > application REST API documentation.")
            .version("v1.0.0")
            .license(new License().name("Apache 2.0")
                .url("https://springdoc.org")))
        .externalDocs(new ExternalDocumentation()
            .description("Learning Platform wiki Documentation")
            .url("https://github.com/upc-is-si729/daos-language-reference"));

    // Add security scheme
    final String securitySchemeName = "bearerAuth";

    openApi.addSecurityItem(new SecurityRequirement()
            .addList(securitySchemeName))
        .components(new Components()
            .addSecuritySchemes(securitySchemeName,
                new SecurityScheme()
                    .name(securitySchemeName)
                    .type(SecurityScheme.Type.HTTP)
                    .scheme("bearer")
                    .bearerFormat("JWT")));

    // Return OpenAPI configuration object with all the settings
    return openApi;
  }
}
```
En el package `infrastructure.persistence.jpa.strategy` crear la clase `SnakeCaseWithPluralizedTablePhysicalNamingStrategy` con el siguiente contenido:

```java
import org.hibernate.boot.model.naming.Identifier;
import org.hibernate.boot.model.naming.PhysicalNamingStrategy;
import org.hibernate.engine.jdbc.env.spi.JdbcEnvironment;

import static io.github.encryptorcode.pluralize.Pluralize.pluralize;

public class SnakeCaseWithPluralizedTablePhysicalNamingStrategy implements PhysicalNamingStrategy {
  @Override
  public Identifier toPhysicalCatalogName(Identifier identifier, JdbcEnvironment jdbcEnvironment) {
    return this.toSnakeCase(identifier);
  }

  @Override
  public Identifier toPhysicalSchemaName(Identifier identifier, JdbcEnvironment jdbcEnvironment) {
    return this.toSnakeCase(identifier);
  }

  @Override
  public Identifier toPhysicalTableName(Identifier identifier, JdbcEnvironment jdbcEnvironment) {

    return this.toSnakeCase(this.toPlural(identifier));
  }

  @Override
  public Identifier toPhysicalSequenceName(Identifier identifier, JdbcEnvironment jdbcEnvironment) {
    return this.toSnakeCase(identifier);
  }

  @Override
  public Identifier toPhysicalColumnName(Identifier identifier, JdbcEnvironment jdbcEnvironment) {
    return this.toSnakeCase(identifier);
  }

  private Identifier toSnakeCase(final Identifier identifier) {
    if (identifier == null) {
      return null;
    }
    final String regex = "([a-z])([A-Z])";
    final String replacement = "$1_$2";
    final String newName = identifier.getText()
            .replaceAll(regex, replacement)
            .toLowerCase();
    return Identifier.toIdentifier(newName);
  }

  private Identifier toPlural(final Identifier identifier) {
    final String newName = pluralize(identifier.getText());
    return Identifier.toIdentifier(newName);
  }
}
```

En el package `interfaces.rest.resources` crear el record `MessageResource` con el siguiente contenido:

```java
public record MessageResource(String message) {
}
```


# Swagger UI

Usa la url  http://localhost:8090/swagger-ui/index.html para ejecutar el API.
