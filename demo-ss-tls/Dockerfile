# Utilisez l'image Maven officielle pour construire le projet
FROM maven:3.8.5-openjdk-17 AS build

# Définissez le répertoire de travail dans le conteneur
WORKDIR /app

# Copiez le fichier pom.xml et téléchargez les dépendances
COPY pom.xml .
RUN mvn dependency:go-offline -B

# Copiez le reste de l'application source
COPY src ./src

# Construisez le projet et créez le fichier JAR
RUN mvn package -DskipTests

# Utilisez l'image JDK pour exécuter le projet
FROM openjdk:17-jdk-alpine

# Définissez le répertoire de travail dans le conteneur
WORKDIR /app

# Copiez le fichier JAR généré dans le conteneur
COPY --from=build /app/target/*.jar app.jar

# Exposez le port sur lequel l'application va s'exécuter
EXPOSE 8080

# Commande pour exécuter l'application
ENTRYPOINT ["java", "-jar", "app.jar"]
