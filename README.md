# Run-Combined-ReactJs-and-Spring-Microservices-by-local-docker

Great! Since my project folders are named **`attendanceMicroService`**, **`ReactJsProject`**, and **`userservice`**, we'll structure the `docker-compose.yml` file accordingly.  

---

### **Updated Project Structure**
```
/project-root
│── /ReactJsProject
│   ├── Dockerfile
│   ├── package.json
│   ├── src/ (React source files)
│── /userservice
│   ├── Dockerfile
│   ├── target/
│   │   ├── userservice-0.0.1-SNAPSHOT.jar
│── /attendanceMicroService
│   ├── Dockerfile
│   ├── target/
│   │   ├── Attendence-0.0.1-SNAPSHOT.jar
│── docker-compose.yml
```

---

### **Dockerfiles**
#### **1️⃣ React App - `/ReactJsProject/Dockerfile`**
```dockerfile
# Use Node.js for building React App
FROM node:22 AS build

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .
RUN npm run build

# Use Nginx to serve the built React App
FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html

EXPOSE 3000
CMD ["nginx", "-g", "daemon off;"]
```

#### **2️⃣ User Service - `/userservice/Dockerfile`**
```dockerfile
FROM openjdk:23-jdk-slim
WORKDIR /app

COPY target/*.jar userservice-0.0.1-SNAPSHOT.jar

EXPOSE 8080
ENTRYPOINT ["java", "-jar", "userservice-0.0.1-SNAPSHOT.jar"]
```

#### **3️⃣ Attendance Microservice - `/attendanceMicroService/Dockerfile`**
```dockerfile
FROM openjdk:23-jdk-slim
WORKDIR /app

COPY target/*.jar Attendence-0.0.1-SNAPSHOT.jar

EXPOSE 8181
ENTRYPOINT ["java", "-jar", "Attendence-0.0.1-SNAPSHOT.jar"]
```

---

### **Updated `docker-compose.yml`**
```yaml
version: '3.8'

services:
  react-app:
    build: ./ReactJsProject
    ports:
      - "3000:80"
    depends_on:
      - userservice
      - attendancemicroservice

  userservice:
    build: ./userservice
    ports:
      - "8080:8080"

  attendancemicroservice:
    build: ./attendanceMicroService
    ports:
      - "8181:8181"
```

---


### **Move to all project root directory where docker-compose.yml exis**
change directory.


### **Run Everything with One Command**
```bash
docker-compose up --build -d
```

✅ This will:
- Automatically build and run **React, User Service, and Attendance Microservice**.
- Start **ReactJS on port `3000`**, **User Service on port `8080`**, and **Attendance Service on port `8181`**.
- Ensure the **React App waits for backend services** before running.

---

### **Manage Running Containers**
Check running containers:
```bash
docker ps
```
Stop all services:
```bash
docker-compose down
```

This setup ensures **clean, structured, and easy management** of all three services! 🚀 Let me know if you need any tweaks.
