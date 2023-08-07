# # Что такое Protector 4 J

[VLINX Protector4J](https://protector4j.com) это инструмент для предотвращения декомпиляции Java-приложений. Protector предоставляет пользовательский собственный загрузчик классов путем изменения JVM. Классы Java шифруются с помощью AES и расшифровываются в собственном загрузчике классов.

# Краткая справка

* **Поддерживается**:

	[InAccel](https://inaccel.com)

* **Источник этого описания**:

	[inaccel/protector4j repo](https://github.com/inaccel/protector4j)

# Как использовать это изображение

Вы можете запустить задачу VLINX Protector 4 J, используя этот образ Docker напрямую, передав параметры задачи Java в `docker run`:

```console
$ docker run -it --rm --name my-java-task -u $(id -u):$(id -g) -v $(pwd):/usr/src/myvlinx -w /usr/src/myvlinx inaccel/protector4j \
	--version <jre-version> \
	--email <account-email> \
	--password <md5-of-password> \
	--protect-all \
	jar-path1 jar-path2 ...
```

## Создание локального образа Docker (необязательно)

Это базовый образ, который вы можете расширить, поэтому в нем есть необходимый минимум пакетов. Если вы добавите пользовательский пакет (ы) в `Dockerfile`, то вы можете создать свой локальный образ Docker следующим образом:

```console
$ docker-compose build
```

# Многоступенчатые сборки

Вы можете создать свое приложение с помощью Maven, защитить его с помощью Protector4J и упаковать все в образ, который не включает ни Maven, ни Protector4J, используя [многоступенчатые сборки](https://docs.docker.com/develop/develop-images/multistage-build ).
```dockerfile
# build
FROM maven
WORKDIR /usr/src/app
COPY pom.xml .
RUN mvn -B -e -C -T 1C org.apache.maven.plugins:maven-dependency-plugin:3.1.1:go-offline
COPY . .
RUN mvn -B -e -o -T 1C verify

# protect
FROM inaccel/protector4j
WORKDIR /usr/src/app
COPY --from=0 /usr/src/app .
ARG VLINX_EMAIL
ARG VLINX_PASSWORD
RUN vlinx-protector4j \
	--version 11 \
	--email ${VLINX_EMAIL} \
	--password ${VLINX_PASSWORD} \
	--protect-all \
	target/*.jar

# package without maven, protector4j
FROM debian
ENV JAVA_HOME="/usr/vlinx/jre"
ENV PATH="${JAVA_HOME}/bin:${PATH}"
COPY --from=1 /usr/src/app/jre ${JAVA_HOME}
COPY --from=1 /usr/src/app/target/*.jar ./
```
![](https://skrinshoter.ru/s/070823/MuycKVhA.jpg)

##### Защитите код Java от декомпиляции
```

Защитите код Java от декомпиляции, зашифровав классы Java, не прибегая к запутыванию.
```

Поддержка приложений JavaSE, Tomcat Web App, Spring Boot App, GlassFish App, Payara App и Java 8, 
Java 11 Environment.
```
Руководство по установке плагина eclipse: https://protector4j.com/docs/eclipse-plugin /
```

После установки, пожалуйста, нажмите Окно -> Показать вид-> Другое ..., затем найдите 
представление Protector4J в диалоговом окне
```

Документация: https://protector4j.com/docs/get-started
```

Сайт: https://protector4j.com
