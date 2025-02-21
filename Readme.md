# Deployment of Virtual Desktop using Apache Guacamole with VNC Server on Oracle Virtual Instance  

This project demonstrates the deployment of **Apache Guacamole** with a **VNC Server** on an **Oracle Virtual Instance**. The solution provides a secure, scalable, and user-friendly remote desktop access system using open-source tools like **MariaDB** and **Tomcat9**.  

---

## Table of Contents  

1. [Project Overview](#project-overview)  
2. [Features](#features)  
3. [Prerequisites](#prerequisites)  
4. [Installation](#installation)  
5. [Configuration](#configuration)  
6. [Usage](#usage)  
7. [Documentation](#documentation)  
8. [Demo Video](#demo-video)  
9. [Contributing](#contributing)  
10. [License](#license)  

---

## Project Overview  

Apache Guacamole is a clientless remote desktop gateway that allows users to access remote systems via a web browser. This project deploys Guacamole on an Oracle Virtual Instance, integrating it with a VNC Server for remote desktop sessions. The deployment uses:  

- **MariaDB**: For user authentication and connection management.  
- **Tomcat9**: As the servlet container for the Guacamole web application.  
- **VNC Server**: To provide remote desktop sessions.  

---

## Features  

- **Web-Based Remote Access**: Access remote desktops via a browser without installing additional software.  
- **Secure Authentication**: User authentication and connection details are stored securely in MariaDB.  
- **Scalable**: Designed to handle multiple concurrent users.  
- **Cost-Effective**: Leverages open-source tools and cloud infrastructure.  
- **User-Friendly**: Simple web interface for both administrators and end-users.  

---

## Prerequisites  

Before starting the installation, ensure you have:  

- An **Oracle Virtual Instance** running Ubuntu 20.04 or later.  
- **Sudo or root privileges** on the instance.  
- Access to ports **8080** (Tomcat9), **3306** (MariaDB), and **5901** (VNC Server).  
- A desktop environment installed (e.g., Xfce) if using VNC Server.  

---

## Installation  

### 1. Install Java  
Apache Guacamole requires Java to run Tomcat9. Install OpenJDK 11:  
```bash
sudo apt update
sudo apt install -y openjdk-11-jdk
```

### 2. Install Tomcat9  
Install Tomcat9 and its admin tools:  
```bash
sudo apt install -y tomcat9 tomcat9-admin
```

### 3. Install MariaDB  
Install MariaDB and secure the installation:  
```bash
sudo apt install -y mariadb-server
sudo mysql_secure_installation
```

### 4. Install Apache Guacamole  
1. Install dependencies and build Guacamole Server:  
   ```bash
   sudo apt install -y build-essential libcairo2-dev libjpeg-turbo8-dev libpng-dev libtool-bin libossp-uuid-dev libavcodec-dev libavutil-dev libswscale-dev freerdp2-dev libpango1.0-dev libssh2-1-dev libtelnet-dev libvncserver-dev libpulse-dev libssl-dev libvorbis-dev libwebp-dev
   wget https://apache.org/dyn/closer.cgi?action=download&filename=guacamole/1.5.3/source/guacamole-server-1.5.3.tar.gz
   tar -xzf guacamole-server-1.5.3.tar.gz
   cd guacamole-server-1.5.3
   ./configure --with-init-dir=/etc/init.d
   make
   sudo make install
   sudo ldconfig
   sudo systemctl daemon-reload
   sudo systemctl start guacd
   sudo systemctl enable guacd
   ```

2. Deploy the Guacamole web application:  
   ```bash
   wget https://apache.org/dyn/closer.cgi?action=download&filename=guacamole/1.5.3/binary/guacamole-1.5.3.war -O guacamole.war
   sudo mv guacamole.war /var/lib/tomcat9/webapps/
   sudo systemctl restart tomcat9
   ```

### 5. Install and Configure VNC Server  
1. Install TigerVNC and a desktop environment:  
   ```bash
   sudo apt install -y tigervnc-standalone-server xfce4 xfce4-goodies
   ```

2. Set up the VNC Server:  
   ```bash
   vncserver
   vncserver -kill :1
   echo '#!/bin/sh
   unset SESSION_MANAGER
   unset DBUS_SESSION_BUS_ADDRESS
   startxfce4 &' > ~/.vnc/xstartup
   chmod +x ~/.vnc/xstartup
   vncserver :1
   ```

---

## Configuration  

### 1. Configure MariaDB for Guacamole  
1. Log into MariaDB and create a database and user:  
   ```sql
   CREATE DATABASE guacamole_db;
   CREATE USER 'guacamole_user'@'localhost' IDENTIFIED BY 'your_password';
   GRANT SELECT, INSERT, UPDATE, DELETE ON guacamole_db.* TO 'guacamole_user'@'localhost';
   FLUSH PRIVILEGES;
   EXIT;
   ```

2. Import the Guacamole schema:  
   ```bash
   cat /path/to/guacamole-auth-jdbc-1.5.3/mysql/schema/*.sql | mysql -u guacamole_user -p guacamole_db
   ```

3. Configure Guacamole to use MariaDB by editing `/etc/guacamole/guacamole.properties`:  
   ```properties
   auth-provider: net.sourceforge.guacamole.net.auth.jdbc.JDBCAuthenticationProvider
   mysql-hostname: localhost
   mysql-port: 3306
   mysql-database: guacamole_db
   mysql-username: guacamole_user
   mysql-password: your_password
   ```

---

## Usage  

1. Access the Guacamole web interface at:  
   ```
   http://<your-instance-ip>:8080/guacamole/
   ```

2. Log in using the credentials configured in MariaDB.  
3. Set up a new VNC connection in Guacamole:  
   - Protocol: VNC  
   - Hostname: `localhost`  
   - Port: `5901`  
   - Password: [VNC Server password]  

---
## Demo Video  

Watch the step-by-step demonstration of the deployment process:  
[YouTube Demo Video](#) ([https://www.youtube.com/watch?v=doS4X0NKnJk]).  
