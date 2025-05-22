
# Instalación de DSpace 8 en Ubuntu 24.04 LTS  
**Guía técnica para implementar repositorios académicos**  

---

## 📋 Prerrequisitos  
- **Sistema operativo**: Ubuntu 24.04 LTS (fresh install recomendado)  
- **Hardware mínimo**:  
  - 8 GB RAM (16 GB recomendado)  
  - 100 GB almacenamiento  
- **Usuario con privilegios sudo**  

---

## 🛠️ Configuración inicial  

### 1. Actualizar sistema  
```
sudo apt update && sudo apt upgrade -y  
```

### 2. Crear usuario dedicado  
```
sudo useradd -m dspace  
sudo passwd dspace  # Establecer contraseña  
sudo usermod -aG sudo dspace  
sudo mkdir /dspace  
```

---

## 🔧 Instalación de dependencias  

### 3. Paquetes esenciales  
```
sudo apt install wget curl git build-essential zip unzip -y  
```

### 4. Java JDK 17  
```
sudo apt install openjdk-17-jdk -y  
echo 'JAVA_HOME="/usr/lib/jvm/java-17-openjdk-amd64"' | sudo tee -a /etc/environment  
source /etc/environment  
```

### 5. Herramientas de construcción  
```
sudo apt install maven ant -y  
```

---

## 🗄️ Base de datos: PostgreSQL  

### 6. Instalar y configurar  
```
sudo apt install postgresql postgresql-contrib -y  
sudo -u postgres psql -c "CREATE USER dspace WITH PASSWORD 'dspace';"  
sudo -u postgres psql -c "CREATE DATABASE dspace OWNER dspace ENCODING 'UNICODE';"  
sudo -u postgres psql -d dspace -c "CREATE EXTENSION pgcrypto;"  
```

---

## 🔍 Motor de búsqueda: Solr  

### 7. Instalar Solr 8.11  
```
wget https://downloads.apache.org/lucene/solr/8.11.4/solr-8.11.4.zip  
unzip solr-8.11.4.zip  
sudo bash solr-8.11.4/bin/install_solr_service.sh solr-8.11.4.zip  
sudo systemctl enable solr  
```

---

## 🐈 Servidor web: Tomcat 10  

### 8. Configurar Tomcat  
```
sudo apt install tomcat10 -y  
sudo sed -i 's/Connector port="8080"/Connector port="8080" URIEncoding="UTF-8"/g' /etc/tomcat10/server.xml  
sudo systemctl restart tomcat10  
```

---

## ⚙️ Instalación de DSpace  

### 9. Descargar y compilar  
```
sudo mkdir /build  
cd /build  
sudo wget https://github.com/DSpace/DSpace/archive/refs/tags/dspace-8.0.zip  
unzip dspace-8.0.zip  
cd DSpace-dspace-8.0  
mvn package  
```

### 10. Despliegue backend  
```
cd dspace/target/dspace-installer  
ant fresh_install  
sudo cp -R /dspace/webapps/* /var/lib/tomcat10/webapps  
```

---

## 🌐 Frontend Angular  

### 11. Instalar Node.js y dependencias  
```
sudo apt install nodejs npm -y  
sudo npm install -g yarn pm2  
```

### 12. Configurar interfaz  
```
cd /home/dspace  
wget https://github.com/DSpace/dspace-angular/archive/refs/tags/dspace-8.0.zip  
unzip dspace-8.0.zip  
cd dspace-angular-dspace-8.0  
yarn install  
yarn run build:prod  
```

### 13. Iniciar servicio  
```
pm2 start ecosystem.config.js  
pm2 save  
pm2 startup  
```

---

## 🔍 Acceso al sistema  
- **Backend**: `http://localhost:8080/server`  
- **Frontend**: `http://localhost:4000`  
- **Admin**: Ejecutar `/dspace/bin/dspace create-administrator`  

---

## 🚨 Solución de problemas  
- **Error de RAM insuficiente**:  
  ```
  sudo fallocate -l 4G /swapfile && sudo chmod 600 /swapfile && sudo mkswap /swapfile && sudo swapon /swapfile  
  ```  
- **Reiniciar servicios críticos**:  
  ```
  sudo systemctl restart postgresql tomcat10 solr  
  ```

---
## 🔗 Recursos oficiales  
Para complementar esta guía, se recomienda consultar:  
- **[Repositorio oficial DSpace](https://github.com/DSpace/DSpace)**  
  Código fuente principal del sistema de repositorios, con documentación técnica y releases oficiales.  

- **[Interfaz Angular de DSpace](https://github.com/DSpace/dspace-angular)**  
  Repositorio del frontend moderno basado en Angular, esencial para personalizaciones de UI/UX.  



---

> **Nota**: Estos recursos se actualizan constantemente - recomiendo monitorear los releases en GitHub para obtener las últimas mejoras de seguridad y funcionalidades.  
 
> **Licencia**: Apache 2.0 | **Versión**: DSpace 7.0 (2024)  

