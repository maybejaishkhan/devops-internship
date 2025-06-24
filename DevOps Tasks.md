## Task — June 24th

### ✅ Subtask 1 — Nodejs + Mongodb App
Create two docker containers: Nodejs and Mongodb. Create a website with backend nodejs and displays data from mongodb database.

1. **Create a docker network** --> `docker network create nodejs-network`
2. **Nodejs folder** --> `mkdir node-mongo && cd node-mongo`
3. **Create 3 files** --> `touch server.js Dockerfile package.json`

```js
// server.js

const express = require('express');
const { MongoClient } = require('mongodb');

const app = express();
const port = 3000;

// The connection string uses the container name 'mongodb' as the host.
const url = 'mongodb://mongodb:27017';
const client = new MongoClient(url);

const dbName = 'myProject';
let db;

async function main() {
    // Connect to the MongoDB cluster
    await client.connect();
    console.log('Connected successfully to MongoDB');
    db = client.db(dbName);
    const collection = db.collection('documents');
	
    // --- Add Initial Data ---
    // This is just to ensure there's some data.
    // In a real app, you might have a separate seeding script.
    const findResult = await collection.find({}).toArray();
    if (findResult.length === 0) {
        console.log('No documents found, inserting initial data...');
        await collection.insertMany([
            { item: 'journal', qty: 25, status: 'A' },
            { item: 'notebook', qty: 50, status: 'A' },
            { item: 'paper', qty: 100, status: 'D' }
        ]);
    }
    // --- End of Data Insertion ---
	
    app.listen(port, () => {
        console.log(`App listening at http://localhost:${port}`);
    });
}

// Route to get data from MongoDB and display it
app.get('/', async (req, res) => {
    if (!db) {
        res.status(500).send("Database not connected");
        return;
    }
    const collection = db.collection('documents');
    const findResult = await collection.find({}).toArray();
    let html = '<h1>Data from MongoDB</h1><ul>';
    findResult.forEach(doc => {
        html += `<li>Item: ${doc.item}, Quantity: ${doc.qty}, Status: ${doc.status}</li>`;
    });
    html += '</ul>';
    res.send(html);
});

main().catch(console.error);
```

```dockerfile
# Dockerfile

FROM node:latest
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD [ "node", "server.js" ]
```

```json
// package.json
{
  "name": "node-mongo-app",
  "version": "1.0.0",
  "description": "A simple Node.js app connecting to MongoDB",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^4.19.2",
    "mongodb": "^6.5.0"
  }
}
```

4. **Run** --> `npm i` to install dependencies.
5. **Build the Image** --> `docker build -t node-mongo .`
6. **Run the two containers** --> *node-mongo* and *mongodb*.

```bash
docker run -d \
--name node-mongo-app \
--network nodejs-network \
-p 3000:3000 node-mongo

docker run -d \
--name mongodb \
--network nodejs-network \
-p 27017:27017 mongo:latest
```

7. Visit [Localhost:3000](localhost:3000) to see the app.

---
### ❌ Subtask 2 — Nginx + Mariadb App
Create two docker containers: Nginx and Mariadb. 

1. **Create a docker network** --> `docker network create nginx-network`
2. **Project folder** --> `mkdir nginx-mariadb && cd nginx-mariadb`
	1. *Nginx Folder* --> `mkdir nginx`
	2. *PHP Folder* --> `mkdir src`
3. **Create 2 files** --> `touch src/index.php nginx/default.conf`

```php
// src/index.php

<!DOCTYPE html>
<html>
<head>
    <title>Nginx + MariaDB App</title>
    <style>
        body { 
	        font-family: sans-serif; 
	        background-color: #f4f4f9; 
	        color: #333; 
		}
        .container { 
	        max-width: 800px; 
	        margin: 40px auto; 
	        padding: 20px; 
	        background: white; 
	        border-radius: 8px; 
	        box-shadow: 0 2px 4px rgba(0,0,0,0.1); 
		}
        h1 { color: #5a67d8; }
        table { width: 100%; border-collapse: collapse; }
        th, td { padding: 12px; border: 1px solid black; text-align: left; }
        th { background-color: #e2e8f0; }
    </style>
</head>
<body>
    <div class="container">
        <h1>Welcome to the LAMP Stack!</h1>
        <p>This page is served by Nginx and PHP, retrieving data from a MariaDB
        container.</p>
		
        <?php
        // Credentials from the MariaDB container's environment variables
        $host = 'mariadb'; // Name of the MariaDB container
        $dbname = 'mydb';
        $user = 'user';
        $pass = 'pass';
		
        try {
            // Establish connection
            $conn = new PDO("mysql:host=$host;dbname=$dbname", $user, $pass);
            $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
            echo "<p>\u2705 Successfully connected to MariaDB database '$dbname'.
            </p>";
			
            // Create table if it doesn't exist
            $conn->exec("CREATE TABLE IF NOT EXISTS inventory (
                id INT(6) UNSIGNED AUTO_INCREMENT PRIMARY KEY,
                product VARCHAR(50) NOT NULL,
                quantity INT(10) NOT NULL
            )");
			
            // Check if table is empty and insert data if it is
            $stmt = $conn->query("SELECT COUNT(*) FROM inventory");
            if ($stmt->fetchColumn() == 0) {
                $conn->exec("INSERT INTO inventory (product, quantity) VALUES
                    ('Laptop', 15),
                    ('Mouse', 150),
                    ('Keyboard', 75),
                    ('Monitor', 40)
                ");
                 echo "<p>Database was empty. Seeded with initial data.</p>";
            }
			
            // Fetch and display data
            echo "<h2>Inventory Data</h2>";
            echo "<table><tr><th>ID</th><th>Product</th><th>Quantity</th></tr>";
            foreach($conn->query("SELECT * FROM inventory") as $row) {
                echo "<tr>";
                echo "<td>" . htmlspecialchars($row['id']) . "</td>";
                echo "<td>" . htmlspecialchars($row['product']) . "</td>";
                echo "<td>" . htmlspecialchars($row['quantity']) . "</td>";
                echo "</tr>";
            }
            echo "</table>";
        } catch(PDOException $e) {
            echo "\u274c Connection failed: " . $e->getMessage();
        } $conn = null; // Close connection
        ?>
    </div>
</body>
</html>
```

```nginx
# nginx/default.conf

server {
    listen 80;
    server_name localhost;
	
    # The document root for the web server
    root /var/www/html;
    index index.php index.html index.htm;
	
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }
	
    # Pass the PHP scripts to FastCGI server listening on php-fpm:9000
    location ~ \.php$ {
        include fastcgi_params;
        # The 'php-fpm' is the container name of our PHP service
        fastcgi_pass php-fpm:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
	
    location ~ /\.ht {
        deny all;
    }
}
```

4. **Create** Dockerfiles --> `touch src/Dockerfile nginx/Dockerfile`
```dockerfile
# src/Dockerfile
FROM php:7.0-fpm
RUN docker-php-ext-install mysqli pdo pdo_mysql
RUN docker-php-ext-enable pdo
```

```dockerfile
# nginx/Dockerfile
FROM nginx
COPY ./default.conf /etc/nginx/conf.d/default.conf
```

5. **Build the images** 
	- `docker build -t php-fpm-pdo src/`
	- `docker build -t nginx-mariadb nginx/`
6. **Run the three containers** --> *nginx* , *mariadb* and *php-fpm*

```bash
docker run -d \
  --name nginx-maria \
  --network nginx-network \
  -p 80:80 \
  nginx:latest

docker run -d \
  --name mariadb \
  --network nginx-network \
  -p 3306:3306 \
  -e MARIADB_ROOT_PASSWORD=rootpass \
  -e MARIADb_DATABASE=mydb \
  -e MARIADB_USER=user \
  -e MARIADB_PASSWORD=pass \
  mariadb:latest

docker run -d \
  --name php-fpm \
  --network nginx-network \
  php-fpm-pdo
```

---
### ✅ Subtask 3 — Configuring Ubuntu Containers via Ansible
Create two Ubuntu containers. Write and push (via SSH) an Ansible playbook from the host machine to configure/install on both containers.

1. **Create 2 Ubuntu containers**
```bash
docker run -itd --name ubuntu-1 -p 2222:22 ubuntu:latest
docker run -itd --name ubuntu-2 -p 2223:22 ubuntu:latest
```

2. **Configure SSH in both containers**
	1. *Access shell* --> `docker exec -it <container> bash`
	2. *Update* --> `apt update && apt upgrade -y`
	3. *Install OpenSSH* --> `apt install -y openssh-server sudo`
	4. *Create user with password* --> `adduser ansibleuser`
	5. *Add user to sudo group* --> `usermod -aG sudo ansibleuser`
	6. **Allow password authentication** (or SSH Keys)
```bash
sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin no/' /etc/ssh/sshd_config
sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config
```

4. **Restart SSH service and exit** --> `service ssh start`
5. **Install Ansible** 
```bash
sudo apt install software-properties-common -y
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible -y
```

6. **Ansible Folder** --> `mkdir ansible && cd ansible`
7. **Create a file** `inventory.ini`
```toml
[ubuntu_containers]
ubuntu-1 ansible_host=127.0.0.1 ansible_port=2222 ansible_user=ansibleuser ansible_ssh_pass=your_password_here
ubuntu-2 ansible_host=127.0.0.1 ansible_port=2223 ansible_user=ansibleuser ansible_ssh_pass=your_password_here
```

8. **Create an ansible playbook** `configure-ubuntu.yaml`
```yaml
---

- name: Configure Ubuntu Containers
  hosts: ubuntu_containers
  become: yes # Run tasks with sudo privileges
  vars:
    # --- NVM and Node.js variables ---
    nvm_version: "0.39.7" # Specify the NVM version
    node_version: "20"    # Specify the Node.js LTS version to install via nvm
    nvm_install_dir: "/usr/local/nvm" # System-wide NVM installation path
    nvm_user: "{{ ansible_user }}" # User to install NVM for (defaults to the SSH user)

  tasks:
    - name: Update apt cache
      ansible.builtin.apt:
        update_cache: yes
        cache_valid_time: 3600 # Cache valid for 1 hour

    - name: Install common prerequisites
      ansible.builtin.apt:
        name:
          - curl
          - build-essential # For compiling Node.js and other packages
          - git
        state: present

    # --- NVM and Node.js Installation ---
    - name: Ensure NVM directory exists for {{ nvm_user }}
      ansible.builtin.file:
        path: "{{ nvm_install_dir }}"
        state: directory
        owner: "{{ nvm_user }}"
        group: "{{ nvm_user }}"
        mode: '0755'

    - name: Download and install NVM for {{ nvm_user }}
      ansible.builtin.shell: |
        curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v{{ nvm_version }}/install.sh | bash
        # Source NVM for the current session to make it available immediately
        export NVM_DIR="{{ nvm_install_dir }}"
        [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
        [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"
      args:
        creates: "{{ nvm_install_dir }}/nvm.sh" # Only run if nvm.sh doesn't exist
      register: nvm_install_output
      changed_when: nvm_install_output.rc != 0
      # Ensure this task runs as the specified user, not root by default 'become: yes'
      become: no
      become_user: "{{ nvm_user }}"

    - name: Add NVM sourcing to .bashrc for {{ nvm_user }}
      ansible.builtin.lineinfile:
        path: "/home/{{ nvm_user }}/.bashrc"
        line: |
          export NVM_DIR="{{ nvm_install_dir }}"
          [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
          [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion" # This loads nvm bash_completion
        insertafter: EOF
        state: present
      become: no
      become_user: "{{ nvm_user }}"

    - name: Install Node.js (LTS version) using NVM for {{ nvm_user }}
      ansible.builtin.shell: |
        export NVM_DIR="{{ nvm_install_dir }}"
        [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
        nvm install {{ node_version }}
        nvm alias default {{ node_version }} # Set as default
        nvm use default
      args:
        creates: "{{ nvm_install_dir }}/versions/node/v{{ node_version }}/bin/node"
      register: node_install_output
      changed_when: node_install_output.rc != 0
      # Ensure this task runs as the specified user, not root by default 'become: yes'
      become: no
      become_user: "{{ nvm_user }}"

    # --- PHP Installation ---
    - name: Install PHP and common extensions
      ansible.builtin.apt:
        name:
          - php
          - php-cli
          - php-fpm # PHP FastCGI Process Manager
          - php-mysql # MySQL support for PHP
          - php-xml # XML support
          - php-mbstring # Multibyte string support
          - php-zip # Zip archive support
          - php-gd # GD image library support
        state: present

    # --- Python Installation ---
    - name: Ensure Python3 and pip are installed
      ansible.builtin.apt:
        name:
          - python3
          - python3-pip
        state: present

    # --- Apache2 Installation and Configuration ---
    - name: Install Apache2 web server
      ansible.builtin.apt:
        name: apache2
        state: present

    - name: Start and enable Apache2 service
      ansible.builtin.service:
        name: apache2
        state: started
        enabled: yes

    - name: Configure Apache to serve a simple PHP test page (optional)
      ansible.builtin.copy:
        content: |
          <?php
          echo "<h1>Hello from Apache and PHP!</h1>";
          echo "<p>PHP Version: " . phpversion() . "</p>";
          ?>
        dest: /var/www/html/info.php
        owner: www-data
        group: www-data
        mode: '0644'
      notify: Restart Apache2

    - name: Enable Apache modules (if needed, e.g., rewrite)
      community.general.apache2_module:
        state: present
        name: rewrite
      notify: Restart Apache2

  handlers:
    - name: Restart Apache2
      ansible.builtin.service:
        name: apache2
        state: restarted

```

9. **Run the playbook** --> `ansible-playbook -i inventory.ini configure_ubuntu.yaml`