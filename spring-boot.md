# Spring Boot Notes
## Script de carga
Para realizar uma carga inicial, basta inserir um arquivo chamado **data.sql** na pasta resources
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

## Banner
Inserir uma imagem chamada banner.png no diretorio resources gera um ASCII colorido
