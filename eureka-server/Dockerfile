# === ETAPA 1: CONSTRUCCIÓN (BUILD) ===
# Usamos una imagen de Maven con JDK 21 para compilar el proyecto
FROM maven:3.9.6-eclipse-temurin-21 AS builder

# Directorio de trabajo dentro del contenedor de build
WORKDIR /app

# Copiamos los archivos necesarios para descargar dependencias primero (Caché de capas)
COPY .mvn/ .mvn
COPY mvnw pom.xml ./

# Damos permisos de ejecución al wrapper y descargamos dependencias offline
RUN chmod +x mvnw && ./mvnw dependency:go-offline

# Copiamos el código fuente
COPY src/ src/

# Compilamos y empaquetamos la aplicación (saltando los tests para agilizar el build de la imagen)
RUN ./mvnw clean package -DskipTests

# === ETAPA 2: EJECUCIÓN (RUNTIME) ===
# Usamos una imagen ligera de JRE 21 (Alpine Linux) para producción
FROM eclipse-temurin:21-jre-alpine

# Directorio de trabajo para la aplicación
WORKDIR /app

# Copiamos el JAR generado en la etapa anterior
COPY --from=builder /app/target/eureka-server-0.0.1-SNAPSHOT.jar app.jar

# Exponemos el puerto configurado en application.properties
EXPOSE 8761

# Comando de inicio
ENTRYPOINT ["java", "-jar", "app.jar"]