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




