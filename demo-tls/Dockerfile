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

# Installez OpenSSL
RUN apk add --no-cache openssl

# Définissez le répertoire de travail dans le conteneur
WORKDIR /app

# Générer une clé privée RSA
RUN openssl genpkey -algorithm RSA -out private_key.pem -pkeyopt rsa_keygen_bits:2048

# Créer une demande de signature de certificat (CSR)
RUN openssl req -new -key private_key.pem -out request.csr -subj "/C=US/ST=NY/L=New York/O=ExampleCorp/OU=IT/CN=localhost"

# Auto-signer le certificat avec la clé privée
RUN openssl x509 -req -in request.csr -signkey private_key.pem -out certificate.crt -days 3650

# Convertir la clé privée et le certificat en un keystore PKCS12
RUN openssl pkcs12 -export -out keystore.p12 -inkey private_key.pem -in certificate.crt -name myalias -passout pass:your_password

# Copiez le fichier JAR généré dans le conteneur
COPY --from=build /app/target/*.jar app.jar

# Exposez le port sur lequel l'application va s'exécuter
EXPOSE 8443

# Commande pour exécuter l'application
ENTRYPOINT ["java", "-jar", "app.jar"]
