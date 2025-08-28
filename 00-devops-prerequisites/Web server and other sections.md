# **Apache webserver**

### 🔹 1. Installation & Service

```bash
yum install httpd   # Installs Apache
service httpd start # Starts Apache
service httpd status # Check status
```

* It’s running fine (`Active: active (running)`).
* Logs show SELinux context enforcement (`httpd_t`), which is normal.

---

### 🔹 2. Logs

* **Access log** → `/var/log/httpd/access_log`

  * Shows incoming requests (`GET / HTTP/1.1` → status `403` = Forbidden).

* **Error log** → `/var/log/httpd/error_log`

  * Important lines:

    * `Could not reliably determine the server's fully qualified domain name` → Fix by setting `ServerName` in `/etc/httpd/conf/httpd.conf` (e.g., `ServerName localhost`).
    * `Cannot serve directory /var/www/html/: No matching DirectoryIndex (index.html) found` → This is why you’re getting **403 Forbidden**.

👉 Apache expects a **default index file** (like `index.html`) in `/var/www/html`. Since it’s missing, and directory listing is disabled, it refuses access.

---

### 🔹 3. Configuration

* Config file: `/etc/httpd/conf/httpd.conf`

  * `Listen 80` → Apache is listening on default port 80.
  * `DocumentRoot "/var/www/html"` → Default location for web files.

To fix your **403 error**, do either:

✅ **Option 1: Add an index.html**

```bash
echo "<h1>Hello from Apache Web Server</h1>" | sudo tee /var/www/html/index.html
```

✅ **Option 2: Allow directory listing** (not recommended for production):
Edit `/etc/httpd/conf/httpd.conf`:

```apache
<Directory "/var/www/html">
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>
```

Then restart Apache:

```bash
systemctl restart httpd
```

---

### 🔹 4. Firewall

You already added the firewall rule:

```bash
firewall-cmd --permanent --add-service=http
firewall-cmd --reload
```

So port 80 should be open.

---

👉 Summary:

* Apache is installed, running, and listening on port 80.
* You’re seeing **403 Forbidden** because there’s no `index.html` in `/var/www/html`.
* Fix: Add a simple HTML file, or enable directory listing.

---
Perfect 👌 Let’s extend your notes to cover **Apache VirtualHost setup**. This is super useful if you want to host multiple websites (e.g., `site1.local`, `site2.local`) on the same server.

Here’s the **Markdown notes** you can keep in your repo:

---

# 🖥️ Apache VirtualHost Setup

## 📌 What is a VirtualHost?

* Allows Apache to serve multiple websites from a single server (by domain name or IP).
* Useful for development (local testing) and production (shared hosting).

---

## 1. 🔧 Create Directories for Sites

```bash
sudo mkdir -p /var/www/site1.local/public_html
sudo mkdir -p /var/www/site2.local/public_html
```

Set permissions:

```bash
sudo chown -R $USER:$USER /var/www/site1.local/public_html
sudo chown -R $USER:$USER /var/www/site2.local/public_html
```

---

## 2. 📝 Add Sample Index Pages

```bash
echo "<h1>Welcome to Site 1</h1>" > /var/www/site1.local/public_html/index.html
echo "<h1>Welcome to Site 2</h1>" > /var/www/site2.local/public_html/index.html
```

---

## 3. ⚙️ Create VirtualHost Config Files

```bash
sudo nano /etc/apache2/sites-available/site1.local.conf
```

Paste:

```apache
<VirtualHost *:80>
    ServerAdmin webmaster@site1.local
    ServerName site1.local
    DocumentRoot /var/www/site1.local/public_html
    ErrorLog ${APACHE_LOG_DIR}/site1_error.log
    CustomLog ${APACHE_LOG_DIR}/site1_access.log combined
</VirtualHost>
```

For site 2:

```bash
sudo nano /etc/apache2/sites-available/site2.local.conf
```

Paste:

```apache
<VirtualHost *:80>
    ServerAdmin webmaster@site2.local
    ServerName site2.local
    DocumentRoot /var/www/site2.local/public_html
    ErrorLog ${APACHE_LOG_DIR}/site2_error.log
    CustomLog ${APACHE_LOG_DIR}/site2_access.log combined
</VirtualHost>
```

---

## 4. ✅ Enable Sites & Reload Apache

```bash
sudo a2ensite site1.local.conf
sudo a2ensite site2.local.conf
sudo systemctl reload apache2
```

---

## 5. 🏠 Update Local Hosts File (for testing)

Edit `/etc/hosts`:

```bash
127.0.0.1   site1.local
127.0.0.1   site2.local
```

---

## 6. 🌍 Test in Browser

* Visit: `http://site1.local`
* Visit: `http://site2.local`

Both should show different index pages.

---

👉 Now you can run multiple sites on one Apache server using VirtualHosts.

## **Apache web serever lab**
I just went through the **Apache installation and deployment process on CentOS Stream 9**, then served static content from `/var/www/html`.

Here are clean notes from That session:

---

# 🖥️ Apache HTTP Server Setup on CentOS Stream 9

## 1. 📦 Install Apache

```bash
sudo yum install -y httpd
```

* Installs Apache (`httpd`) and dependencies (`apr`, `apr-util`, `mod_http2`, etc.).

---

## 2. ▶️ Manage the Apache Service

```bash
# Start Apache
sudo systemctl start httpd

# Enable at boot (optional)
sudo systemctl enable httpd

# Check status
sudo systemctl status httpd
```

✅ Status should show:

* `Active: active (running)`
* `Server configured, listening on: port 80`

⚠️ Common typo to avoid: `syatemctl` → correct is `systemctl`.

---

## 3. 🌍 Default Document Root

* By default, Apache serves files from:

```bash
/var/www/html/
```

* Move or copy your website files:

```bash
sudo mv /opt/food/* /var/www/html/
```

Now Apache serves those files on port `80`.

---

## 4. 🔥 Firewall (if not already open)

```bash
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --reload
```

---

## 5. 📄 Logs (useful for debugging)

* Access log → `/var/log/httpd/access_log`
* Error log → `/var/log/httpd/error_log`

---

✅ At this point, visiting the server’s **IP address** in a browser should display the content you moved to `/var/www/html/`.

---

👉 Next step could be:

* Configuring **VirtualHosts** (multiple sites).
* Or setting up **SSL (https)**.
  
--- 
  # **Apache TOMCAT**

---

# **Tomcat Setup & Deploying WAR File (Port Change + Sample App)**

### **1. Change Tomcat Port**

* Edited `server.xml` to change Tomcat HTTP port:

  ```bash
  sudo sed -i 's/8081/9090/g' /opt/apache-tomcat-11/conf/server.xml
  ```

### **2. Restart Tomcat**

* Stop Tomcat:

  ```bash
  sudo /opt/apache-tomcat-11/bin/shutdown.sh
  ```

* Start Tomcat again:

  ```bash
  sudo /opt/apache-tomcat-11/bin/startup.sh
  ```

* Verified with:

  ```bash
  curl localhost:9090
  ```

  → Shows **Tomcat default homepage** ✅

* Verified Tomcat process:

  ```bash
  ps -ef | grep tomcat
  ```

---

### **3. Deploy WAR File**

* Moved `sample.war` into Tomcat webapps directory:

  ```bash
  cd /opt/
  sudo mv /opt/sample.war /opt/apache-tomcat-11/webapps/
  ```

* Checked app status:

  ```bash
  curl http://localhost:9090/sample/index.html
  ```

  → Result: **HTTP 404 (Not Found)** ❌

---

### **4. Why 404 Happened?**

* When you place a `.war` file in `/webapps/`, Tomcat **automatically extracts** it into a folder (`/webapps/sample/`) when started.
* If you don’t restart Tomcat or if extraction fails, the app won’t be accessible.
* So after moving `sample.war`, you usually need to:

  ```bash
  sudo /opt/apache-tomcat-11/bin/shutdown.sh
  sudo /opt/apache-tomcat-11/bin/startup.sh
  ```
* Then check if the folder `/opt/apache-tomcat-11/webapps/sample/` was created.

---

✅ **Summary Notes**:

* Changed Tomcat port → `9090`.
* Restarted Tomcat → verified running.
* Deployed `sample.war` → app didn’t auto-extract → got **404 error**.
* Next step: restart Tomcat and check if `/webapps/sample/` exists.

---
# **Python**

---

# 🚀 DevOps Notes – Flask Application Deployment

## 📂 Typical Project Structure

```
my-application/
├── LICENSE
├── README.md
├── requirements.txt
├── main.py
├── utils/
├── tests/
├── config/
├── routes/
├── services/
├── db/
└── core/
```

* **`requirements.txt`** → Lists Python dependencies.
* **`main.py`** → Entry point for the Flask app.
* **`utils/`, `routes/`, `services/`, `db/`, `core/`** → Organized modular code.
* **`tests/`** → Unit/integration tests.

---

## 🐍 Running Flask Locally (Development)

Install dependencies:

```bash
pip install -r requirements.txt
```

Run the app:

```bash
python main.py
```

Sample logs:

```
* Serving Flask app "main" (lazy loading)
* Environment: production
  WARNING: This is a development server. Do not use it in production!
* Debug mode: off
* Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

---

## ⚠️ Flask Development Server Warning

The built-in Flask server (`app.run()`) is **not production-ready**.
Instead, use a **WSGI server** to handle production traffic.

---

## ✅ Production Deployment Options

### Common WSGI/ASGI Servers:

* **Gunicorn** → Popular Python WSGI server.
* **uWSGI** → Feature-rich, production-grade WSGI server.
* **Gevent** → Asynchronous networking library.
* **Twisted Web** → Event-driven networking engine.

---

## 🚀 Running Flask in Production with Gunicorn

Install Gunicorn:

```bash
pip install gunicorn
```

Run app with Gunicorn:

```bash
gunicorn main:app
```

* `main` → Python file (`main.py`)
* `app` → Flask object inside the file

Example:

```bash
gunicorn -w 4 -b 0.0.0.0:8000 main:app
```

* `-w 4` → 4 worker processes
* `-b 0.0.0.0:8000` → bind to all network interfaces at port 8000

---

## 🌐 Production Best Practices

1. **Use Gunicorn/uWSGI with Nginx**

   * Nginx handles static files, reverse proxy, SSL/TLS.
   * Gunicorn/uWSGI handles Python app requests.

2. **Environment Variables**

   * Store secrets & configs (`.env`, Vault, or config management tools).

3. **Containerization (Docker)**

   * Package app with dependencies into an image.
   * Deploy consistently across environments.

4. **Process Management**

   * Use `systemd`, `supervisor`, or containers to keep the app running.

5. **Scaling**

   * Horizontal scaling with multiple Gunicorn workers/containers.
   * Orchestrate with **Kubernetes / Docker Swarm**.

---

## 📌 Quick Reference

* Development:

  ```bash
  python main.py
  ```
* Production (Gunicorn):

  ```bash
  gunicorn -w 4 -b 0.0.0.0:8000 main:app
  ```
* With Nginx (reverse proxy):

  * Forward requests from `https://example.com` → `http://127.0.0.1:8000`.

---
## **Python lab**

# 🚀 Flask App Deployment with Gunicorn – Notes

## 1️⃣ Clone the Repository

```bash
cd /opt/
sudo git clone https://github.com/mmumshad/simple-webapp-flask
cd simple-webapp-flask
```

---

## 2️⃣ Install Dependencies

Install Python requirements:

```bash
sudo pip install -r requirements.txt
```

⚠️ **Best Practice:** Use a **virtual environment** instead of running `pip` as root:

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

---

## 3️⃣ Run Flask App (Development Mode)

```bash
python3 app.py
```

Logs:

```
* Running on http://127.0.0.1:5000
* WARNING: This is a development server. Do not use it in production!
```

---

## 4️⃣ Run Flask with Gunicorn (Production WSGI)

### Basic run:

```bash
gunicorn --bind 0.0.0.0:8000 app:app
```

* `app:app` → `app.py` file, inside it Flask object named `app`.
* `--bind 0.0.0.0:8000` → Bind to all interfaces on port 8000.

### With multiple workers:

```bash
gunicorn app:app -w 3
```

* `-w 3` → Run 3 worker processes for concurrency.

---

## 5️⃣ Run in Background

```bash
nohup gunicorn app:app -w 3 &
```

* `nohup` → Keep running even after logout.
* Output logged to `nohup.out`.

Check logs:

```bash
tail -f ~/nohup.out
```

---

## 6️⃣ Verify the Service

```bash
curl localhost:8000
```

Expected output:

```
Welcome!
```

Check running processes:

```bash
ps -ef | grep gunicorn | grep -v grep
```

Example:

```
thor  3116  ... gunicorn app:app -w 3
thor  3123  ... gunicorn worker
thor  3124  ... gunicorn worker
thor  3125  ... gunicorn worker
```

---

## 7️⃣ Common Issues You Faced

* ❌ `app.run` not found → Correct way is `python3 app.py`.
* ❌ Podman vs Docker confusion → Podman emulates Docker CLI, but UID mapping broke.
* ❌ `--allow-root` not supported in Gunicorn → Use a proper user instead of root.
* ⚠️ Pip root warning → Always prefer **virtualenv**.

---

## ✅ Best Practices for Production

1. **Gunicorn + Nginx** → Use Nginx as reverse proxy in front of Gunicorn.
2. **Systemd service** → Run Gunicorn as a managed background service.
3. **Virtualenv/Docker** → Isolate dependencies.
4. **Scaling** → Use multiple workers or containers.
5. **Logs & Monitoring** → Use `systemd`, `supervisor`, or tools like Prometheus/Grafana for visibility.

---

## 📌 Quick Reference

* Development:

  ```bash
  python3 app.py
  ```
* Gunicorn (foreground):

  ```bash
  gunicorn --bind 0.0.0.0:8000 app:app
  ```
* Gunicorn (background with workers):

  ```bash
  nohup gunicorn app:app -w 3 &
  ```
* Check:

  ```bash
  curl localhost:8000
  ps -ef | grep gunicorn
  ```
---
# **NodeJS - Deploy Express App**

# 🚀 NodeJS 

## 📂 Project Structure

```
my-application/
├── LICENSE
├── README.md
├── package.json
├── app.js
├── public/
├── tests/
├── config/
├── routes/
├── services/
└── core/
```

---

## 📦 Dependencies & Scripts (package.json)

Example snippet from **package.json**:

```json
{
  "name": "my-application",
  "version": "0.0.0",
  "private": true,
  "dependencies": {
    "dotenv": "^x.x.x",
    "execa": "^x.x.x",
    "express": "4.16.2"
  },
  "scripts": {
    "debug": "node debug app.js",
    "start": "NODE_ENV=production node app.js",
    "start:dev": "NODE_ENV=dev node app.js",
    "test:e2e": "node tests/run-e2e-test.js",
    "test:unit": "jest tests/unit",
    "test:unit:watch": "jest tests/unit --watch"
  }
}
```

---

## ▶️ Run Application

### Install dependencies

```bash
npm install
```

### Run locally

```bash
node app.js
```

### Run with npm scripts

```bash
# Production mode
npm run start

# Development mode
npm run start:dev
```

---

## ⚙️ Process Management (Production Ready)

In production, don’t run `node app.js` directly.
Instead, use a **process manager** for reliability, logging, and restarts.

### Options:

* **supervisord** – generic process manager
* **forever** – keeps Node.js script running
* **pm2** – most popular for Node.js (recommended ✅)

### PM2 Commands

```bash
# Start single instance
pm2 start app.js

# Start app with 4 clustered instances (better for multi-core CPUs)
pm2 start app.js -i 4

# Show running processes
pm2 list

# Restart / Stop / Delete apps
pm2 restart app_name
pm2 stop app_name
pm2 delete app_name

# Save process list (so it restarts on reboot)
pm2 save

# Startup script
pm2 startup
```

---

## 🔑 Key DevOps Takeaways

* **Development**: `node app.js` or `npm run start:dev`
* **Production**: use `pm2`, `supervisord`, or `forever`
* **Clustering**: PM2 `-i` flag allows running multiple processes on multi-core systems
* **Environment variables**: use `NODE_ENV` and `.env` (with `dotenv`)

---
## **Node JS lab**

# 🚀 DevOps Notes – Deploy NodeJS App with PM2

## 📂 Clone Repository

```bash
cd /opt
sudo git clone https://github.com/contentful/the-example-app.nodejs
cd the-example-app.nodejs
```

---

## 📦 Install Dependencies

```bash
sudo npm install
```

⚠️ Expect some warnings (`deprecated` packages, `old lockfile`).
Run audits if needed:

```bash
npm audit fix         # safe fixes
npm audit fix --force # breaking changes
```

---

## 📜 package.json (Important Sections)

### Dependencies

```json
"dependencies": {
  "express": "^4.16.2",
  "dotenv": "^5.0.0",
  "helmet": "^3.11.0",
  "lodash": "^4.17.5",
  "pug": "~2.0.0-beta6"
}
```

### Scripts

```json
"scripts": {
  "start:watch": "nodemon ./bin/www --ignore public/",
  "start:dev": "node ./bin/www",
  "start": "NODE_ENV=production node ./bin/www",
  "test:unit": "jest test/unit",
  "test:integration": "jest test/integration",
  "test:e2e": "node test/run-e2e-test.js"
}
```

---

## ▶️ Running the App (Dev & Prod)

* Development:

  ```bash
  npm run start:dev
  ```
* Production:

  ```bash
  npm run start
  ```

Server listens on:

```
http://localhost:3000
```

---

## 🔄 Manage App with PM2

### Install PM2 Globally

```bash
sudo npm install pm2@latest -g
```

### Start App with PM2

```bash
pm2 start app.js        # single instance
pm2 start app.js -i 4   # cluster mode with 4 instances
```

### PM2 Management

```bash
pm2 list        # show running apps
pm2 logs        # view logs
pm2 delete app  # stop & remove app
pm2 restart app # restart app
pm2 stop app    # stop app but keep in PM2 list
```

### Auto-start on Reboot

```bash
pm2 startup
pm2 save
```

---

## ✅ Summary

* You cloned the repo and installed dependencies.
* You ran the app in **dev mode** with `npm run start:dev`.
* You deployed in **production mode** using **PM2**, including **cluster mode (4 instances)**.
* PM2 ensures:

  * App auto-restarts on crash
  * Load-balancing across CPU cores
  * Centralized monitoring (`pm2 monit`)

⚡ This is basically a **mini deployment playbook for NodeJS apps with PM2**.

---
# **IP Addresses and Ports (Web Application Perspective)**


# 🌐 Networking & Flask Basics

## 🔹 IP Addresses in Linux

* To view all network interfaces and IP addresses:

  ```bash
  ip addr show
  ```
* Example output:

  ```
  2: enp0s3: <BROADCAST,MULTICAST,UP> mtu 1500 state UP
      inet 10.0.2.15/24 brd 10.0.2.255 scope global enp0s3
  3: wlp0s2: <BROADCAST,MULTICAST,UP> mtu 1500 state UP
      inet 10.0.2.16/24 brd 10.0.2.255 scope global wlp0s2
  ```

### Key Points

* `enp0s3` → Wired/Ethernet interface
* `wlp0s2` → Wireless/Wi-Fi interface
* `inet 10.0.2.15/24` → IPv4 address with subnet mask `/24`
* `127.0.0.1` → Loopback address (default if no host is specified)

---

## 🔹 Ports

* A **port** allows multiple services to run on the same machine but be accessible separately.
* Example:

  * Port `5000` → Flask app
  * Port `22` → SSH
  * Port `80` → HTTP (web traffic)
  * Port `443` → HTTPS (secure web traffic)

---

## 🔹 Flask Example

### Basic Flask App (`main.py`)

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return 'Hello, World!'

if __name__ == "__main__":
    # Run Flask app on a specific IP & Port
    app.run(host="10.0.2.15", port=5000)
```

### Key Points:

* `host="127.0.0.1"` → Default; app only accessible on **local machine**.
* `host="0.0.0.0"` → App accessible from **any network interface**.
* `host="10.0.2.15"` → App bound to **specific IP address**.
* `port=5000` → Flask default port, can be changed.

---

✅ **Takeaway for DevOps:**

* Always know your server’s **IP + Port** combo to access services.
* Binding Flask (or any service) to `0.0.0.0` makes it accessible externally, but be mindful of **security risks**.

---
## **IP adreesses and orts lab**
Perfect case for **notes time** 👌 — you went through Tomcat + Flask setup, port changes, SSH issues, and key management. Let’s turn this into **clean DevOps notes (Markdown)** so next time you avoid the same pain.

---

# Tomcat, Flask & SSH

## 🔹 Change Tomcat Port

```bash
cd /opt/
sudo sed -i 's/8080/9090/g' apache-tomcat-8.5.53/conf/server.xml
sudo ./apache-tomcat-8.5.53/bin/startup.sh
```

**Output:**

```
Using CATALINA_BASE:   /opt/apache-tomcat-8.5.53
Tomcat started.
```

✅ Tomcat now runs on **port 9090**.

---

## 🔹 Flask App Deployment

### Flask App (`/opt/simple-webapp-flask/app.py`)

```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def main():
    return "Welcome!"

@app.route("/how are you")
def hello():
    return "I am good, how about you?"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8080)
```

### Modify Host/Port

* Change **host** & **port** dynamically with `sed`:

```bash
sudo sed -i 's/0.0.0.0/127.0.0.1/g; s/8080/5000/g' /opt/simple-webapp-flask/app.py
```

### Run Flask in Background

```bash
cd /opt/simple-webapp-flask/
nohup python3 app.py &
```

* Output goes to: `~/nohup.out`
* Stop process:

```bash
pkill python3
```

---

## 🔹 SSH Access & Keys

### Problem

* When reconnecting via SSH:

  ```
  Permission denied, please try again.
  ```
* Issue: No SSH keys / wrong permissions.

### Generate SSH Key

```bash
ssh-keygen -t rsa -b 2048
```

* Default save location → `/home/thor/.ssh/id_rsa`
* Always press **Enter** for default path unless you know what you’re doing.

⚠️ **Mistake:** You tried saving key as `thor` in current folder → caused *Permission denied*.
✅ Always save in **`~/.ssh/`**.

### Copy Key to Remote

```bash
ssh-copy-id thor@app01
```

This installs your public key → no more password prompts.

---

## 🔹 Key Lessons Learned

1. **Tomcat Port Change** → Modify `server.xml`, restart Tomcat.
2. **Flask Host Binding**

   * `127.0.0.1` → Local only.
   * `0.0.0.0` → Accessible externally.
3. **nohup for Background Jobs** → Prevents Flask from stopping after logout.
4. **SSH Best Practice** → Always generate & use keys inside `~/.ssh/`.
5. **Kill Stale Processes** → `pkill python3` before restarting apps.

---

# **Dtabases**

## MY SQL

# 🐬 MySQL Setup & User Management Notes

## 🔹 1. Install MySQL

```bash
# Add MySQL repo & install server
wget https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
rpm -ivh mysql80-community-release-el7-3.noarch.rpm
yum install mysql-server -y

# Start service
service mysqld start
service mysqld status
```

✅ Check status:

```
active (running) since ...
Server is operational
```

---

## 🔹 2. Validate Logs & Temporary Root Password

```bash
cat /var/log/mysqld.log
```

Example log snippet:

```
Temporary password for root@localhost: 9/10'pFtE77n
```

---

## 🔹 3. Login with Root

```bash
mysql -u root -p'9/10pFtE77n'
```

⚠️ Warning: Command-line password is insecure. Use prompt when possible.

---

## 🔹 4. Secure Installation

Run:

```bash
mysql_secure_installation
```

* Set strong root password
* Remove anonymous users
* Disallow remote root login
* Remove test database

---

## 🔹 5. Change Root Password

```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';
```

---

## 🔹 6. Database & Table Creation

```sql
-- Create DB
CREATE DATABASE school;
USE school;

-- Create table
CREATE TABLE persons (
    Name VARCHAR(255),
    Age INT,
    Location VARCHAR(255)
);

-- Verify
SHOW TABLES;
```

✅ Insert Data:

```sql
INSERT INTO persons VALUES ("John Doe", 45, "New York");
SELECT * FROM persons;
```

**Result:**

| Name     | Age | Location |
| -------- | --- | -------- |
| John Doe | 45  | New York |

---

## 🔹 7. Create Users

```sql
-- Local user
CREATE USER 'john'@'localhost' IDENTIFIED BY 'MyNewPass4!';

-- Remote specific IP
CREATE USER 'john'@'192.168.1.10' IDENTIFIED BY 'MyNewPass4!';

-- Remote from anywhere
CREATE USER 'john'@'%' IDENTIFIED BY 'MyNewPass4!';
```

✅ Test login:

```bash
mysql -u john -p'MyNewPass4!' -h 192.168.1.10
```

---

## 🔹 8. Grant Privileges

### Minimal Privileges (Fine-grained)

```sql
GRANT SELECT ON school.persons TO 'john'@'localhost';
GRANT SELECT, UPDATE ON school.persons TO 'john'@'192.168.1.10';
```

### Database-wide

```sql
GRANT SELECT, UPDATE ON school.* TO 'john'@'%';
```

### Full Access

```sql
GRANT ALL PRIVILEGES ON *.* TO 'john'@'localhost' WITH GRANT OPTION;
```

Check grants:

```sql
SHOW GRANTS FOR 'john'@'localhost';
```

---

## 🔹 9. Privilege Levels (Cheat Sheet)

* **ALL PRIVILEGES** → Full DB control
* **CREATE** → Create databases/tables
* **DROP** → Delete databases/tables
* **DELETE** → Delete rows
* **INSERT** → Insert new rows
* **SELECT** → Read/query
* **UPDATE** → Modify rows

---

# ✅ Key Takeaways

1. **Default root password** is in `/var/log/mysqld.log`.
2. Always run `mysql_secure_installation` after fresh install.
3. Create separate users per app/service (never use root).
4. Limit user privileges (Principle of Least Privilege).
5. Use `%` only if app needs remote access — otherwise keep to `localhost`.
6. Remember: MySQL listens on port **3306** by default.

---
## **MY SQL lab**

# **Mongo DB**


# 📒 MongoDB Technical Notes

## 🔹 What is MongoDB?

* **Open Source**
* **NoSQL Database** → document-oriented (stores data in JSON-like format)
* **Scalable** → supports sharding, replication
* **High Performance** → optimized for modern applications
* Available in:

  * **Cloud** (Atlas, managed service)
  * **On-Premises** (Community Server)

---

## 🔹 Installation (CentOS/RHEL example)

1. Add MongoDB Yum Repository:

   ```bash
   vi /etc/yum.repos.d/mongodb-org-4.2.repo
   ```

   Add:

   ```ini
   [mongodb-org-4.2]
   name=MongoDB Repository
   baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.2/x86_64/
   gpgcheck=1
   enabled=1
   gpgkey=https://www.mongodb.org/static/pgp/server-4.2.asc
   ```

2. Install MongoDB:

   ```bash
   yum install -y mongodb-org
   ```

---

## 🔹 Start & Check MongoDB

```bash
systemctl start mongod
systemctl status mongod
systemctl enable mongod
```

📌 Logs:

```bash
cat /var/log/mongodb/mongod.log
```

Default port → **27017**
Socket → `/tmp/mongodb-27017.sock`

---

## 🔹 Configuration

File: `/etc/mongod.conf`

```yaml
systemLog:
  destination: file
  logAppend: true
  path: /var/log/mongodb/mongod.log

storage:
  dbPath: /var/lib/mongo
  journal:
    enabled: true
```

---

## 🔹 Mongo Shell

Connect:

```bash
mongo
```

Check version:

```bash
db.version()
```

List databases:

```bash
show dbs
```

---

## 🔹 Database & Collection Operations

```javascript
// Use database (creates if not exists)
use school

// Create collection
db.createCollection("persons")

// Insert document
db.persons.insert({
  "name": "John Doe",
  "age": 45,
  "location": "New York",
  "salary": 5000
})

// Show collections
show collections

// Query documents
db.persons.find()

// Find specific
db.persons.find({ "name": "John Doe" })
```

---

## 🔹 Important Notes

* Default config → **no access control** (anyone can read/write).
  ✅ Must enable **authentication** in production.
* Data stored in `/var/lib/mongo`
* Logs stored in `/var/log/mongodb/`

---
## **Mongo DB lab**

# 📒 MongoDB 6.0 Installation & Setup (RHEL/CentOS 9)

## 🔹 1. Add MongoDB Yum Repository

```bash
sudo tee /etc/yum.repos.d/mongodb-org-6.0.repo <<EOF
[mongodb-org-6.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/\$releasever/mongodb-org/6.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-6.0.asc
EOF
```

---

## 🔹 2. Install MongoDB

```bash
sudo yum install -y mongodb-org
```

This installs:

* **mongod** → MongoDB server
* **mongos** → MongoDB sharding router
* **mongosh** → MongoDB shell
* **database tools** → import/export, backup/restore utilities

---

## 🔹 3. Start & Enable MongoDB

```bash
sudo systemctl start mongod
sudo systemctl enable mongod
```

Check status:

```bash
sudo systemctl status mongod
```

---

## 🔹 4. Logs & Default Ports

* **Logs**: `/var/log/mongodb/mongod.log`
* **Data path**: `/var/lib/mongo`
* **Port**: `27017`

## Check Installed MongoDB Version
```bash
mongod --version
```

## Output Example:
```bash
db version v6.0.26
Build Info: {
   "version": "6.0.26",
   "gitVersion": "0c4ec4b6005f75582ce208fc800f09f561b6c2e8",
   "openSSLVersion": "OpenSSL 3.2.2 4 Jun 2024",
   "modules": [],
   "allocator": "tcmalloc",
   "environment": {
       "distmod": "rhel90",
       "distarch": "x86_64",
       "target_arch": "x86_64"
   }
}
```
---
# **Security**
## **SSL  and TLS Basics**
these are the last thigng so dont crete note for it

then i will be setup my lab ecvironment




