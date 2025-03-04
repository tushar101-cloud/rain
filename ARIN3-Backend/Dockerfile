# Build stage for Java backend
FROM eclipse-temurin:17-jdk-alpine as backend-build
WORKDIR /app/backend
COPY ARIN3-Backend .
RUN ./mvnw clean install -DskipTests

# Build stage for Node.js frontend
FROM node:18-alpine as frontend-build
WORKDIR /app/frontend
COPY package*.json ./
COPY app.js ./
COPY index.html ./
COPY styles.css ./
COPY models/ ./models/
RUN npm install

# Final stage
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app

# Copy backend jar
COPY --from=backend-build /app/backend/target/backend-1.0.0.jar ./backend.jar

# Copy frontend files
COPY --from=frontend-build /app/frontend ./public

# Copy server.js for serving both backend and frontend
COPY server.js .

# Install Node.js in the final image
RUN apk add --update nodejs npm

# Install Express
RUN npm init -y && npm install express

# Expose port
EXPOSE 8080

# Start both backend and frontend
CMD java -jar backend.jar & node server.js
