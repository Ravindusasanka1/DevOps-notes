## **Build and packaging**
---
# ☕ Java Commands – Quick Notes

## 🔹 Check Java Version

```bash
java -version
```

Output example:

```
java version "21.0.8" 2025-07-15 LTS
Java(TM) SE Runtime Environment (build 21.0.8+12-LTS-250)
Java HotSpot(TM) 64-Bit Server VM (build 21.0.8+12-LTS-250, mixed mode, sharing)
```

---

## 🔹 Compile a Java Program

Navigate to your project directory:

```bash
cd /opt/app/
javac MyClass.java
```

* `javac` = Java compiler
* Produces `MyClass.class` (bytecode)

---

## 🔹 Run a Java Program

```bash
cd /opt/app/
java MyClass
```

Output:

```
Hello Kodekloud
```

---

## 🔹 Generate Javadoc Documentation

```bash
javadoc -d doc MyClass.java
```

* `-d doc` → Generate documentation into `doc/` folder.
* Creates multiple files including:

  * `doc/MyClass.html`
  * `doc/index.html`
  * `doc/help-doc.html`

---

## ✅ Key Notes

* Always use `javac` (not `jvac`) to compile.
* `.java` → compiled to `.class` → executed with `java`.
* Javadoc helps generate **HTML documentation** from Java source.

---

