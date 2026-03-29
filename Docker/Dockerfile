# ---------- Build Stage ----------
FROM node:18-alpine AS builder

WORKDIR /app

COPY package*.json ./
RUN npm install --production #Executes during Build Process

COPY . .

# ---------- Runtime Stage ----------
FROM node:18-alpine

WORKDIR /app

# create non-root user
RUN addgroup app && adduser -S -G app app

COPY --from=builder /app /app

USER app

EXPOSE 3000

CMD ["node", "app.js"]



---

# Stage 1: Build the application
FROM maven:3.9.6-eclipse-temurin-17 AS builder

WORKDIR /build
COPY pom.xml .  #Better than ADD if file is locally present 
COPY src ./src
RUN mvn clean package -DskipTests  #BuildStep

# Stage 2: Run the application
FROM eclipse-temurin:17-jre-slim

WORKDIR /app
COPY --from=builder /build/target/myapp.jar app.jar  #created after mvn build

# Run as non-root user for security
RUN useradd -m appuser  # -m create home directory for the user
USER appuser

EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]    