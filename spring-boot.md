# Spring Boot Notes

## Banner
Inserir uma imagem chamada banner.png no diretorio resources gera um ASCII colorido

## Common Application Properties
 Algumas propriedades do `application.properties` estão descritas da [documentação](https://docs.spring.io/spring-boot/docs/current/reference/html/common-application-properties.html)

## H2 para desenvolvimento
Para desenvolvimento eh bom usar o banco em memoria H2.
Para acessar o console do H2 podemos configurar no application.properties.
```
# h2
spring.h2.console.enabled=true
spring.h2.console.path=/h2
# jpa
spring.datasource.url=jdbc:h2:mem:nome_banco
```
## Script de carga
Para realizar uma carga inicial ou criar schema, basta inserir um arquivo chamado **data.sql** ou **schema.sql** na pasta resources. Para definir criação de schema por plataforma (hsqldb, h2, oracle, mysql, postgresql) basta definir a propriedade `spring.datasource.platform` e os arquivos como o nome 
schema-${platform}.sql e data-${platform}.sql. Isso permite a criação de scrips específicos para algum tipo de banco.

## DEVTOOLS
O devtools é um módulo nativo do Spring Boot que oferece
algumas vantagens interessantes:
* **Restart automático** — Ao alterar uma classe, o Spring
Boot faz o restart do seu contexto rapidamente;
* **Debug remoto** — Permite fazer um debug remoto em
uma porta específica configurada nas propriedades;
* **LiveReload** — Ativa a opção de LiveReload, em que o
browser carrega automaticamente a página toda vez
que o seu código-fonte for alterado.

Para ativar o Devtools basta adicionar a dependencia no pom.xml
```xml
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-devtools</artifactId>
<optional>true</optional>
</dependency>
```
## Docker
[Docker Maven plugin](https://github.com/spotify/dockerfile-maven) permite integração de Spring Boot e Docker
```xml
<plugin>
  <groupId>com.spotify</groupId>
  <artifactId>dockerfile-maven-plugin</artifactId>
  <version>${dockerfile-maven-version}</version>
  <executions>
    <execution>
      <id>default</id>
      <goals>
        <goal>build</goal>
        <goal>push</goal>
      </goals>
    </execution>
  </executions>
  <configuration>
    <repository>spotify/foobar</repository>
    <tag>${project.version}</tag>
    <buildArgs>
      <JAR_FILE>${project.build.finalName}.jar</JAR_FILE>
    </buildArgs>
  </configuration>
</plugin>
```
Um Dockerfile de exemplo:
```Dockerfile
FROM openjdk:8-jre
MAINTAINER David Flemström <dflemstr@spotify.com>

ENTRYPOINT ["/usr/bin/java", "-jar", "/usr/share/myservice/myservice.jar"]

# Add Maven dependencies (not shaded into the artifact; Docker-cached)
ADD target/lib           /usr/share/myservice/lib
# Add the service itself
ARG JAR_FILE
ADD target/${JAR_FILE} /usr/share/myservice/myservice.jar
```
Para realizar o build e fazer o push da imagem: _mvn deploy_

## Actuator
[Actuator](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints) é um sub-projeto do Spring, que disponibiliza as seguintes opções:
*  **actuator** - Lista todos os links disponíveis;
* **dump** - Faz um thread dump;
* **env** - Mostra properties do ConfigurableEnvironment do Spring;
* **health** - Mostra informações do status da aplicação;
* **metrics** - Mostra métricas da aplicação;
* **mappings** - Exibe os caminhos dos @RequestMapping ;
* **trace**  - Exibe o trace dos últimos 100 requests HTTP.
```xml
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```