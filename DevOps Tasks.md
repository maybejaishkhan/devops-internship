## Task — June 24th

### Subtask 1 — Nodejs + Mongodb App
Create two docker containers: Nodejs and Mongodb. Create a website with backend nodejs and displays data from mongodb database.

1. **Create a docker network** --> `docker network create nodejs-network`
2. **Nodejs folder** --> `mkdir node-mongo && cd node-mongo`
3. **Create 2 files** --> `touch server.js package.json`

```js
// server.js

const express = require('express');
const { MongoClient, ObjectId } = require('mongodb');
const bodyParser = require('body-parser');

const app = express();
const port = 3000;

const url = 'mongodb://mongodb:27017';
const client = new MongoClient(url);
const dbName = 'myProject';
let db;

app.use(bodyParser.urlencoded({ extended: true }));

async function main() {
    await client.connect();
    console.log('Connected successfully to MongoDB');
    db = client.db(dbName);

    app.listen(port, () => {
        console.log(`App listening at http://localhost:${port}`);
    });
}

app.get('/', async (req, res) => {
    const collection = db.collection('documents');
    const documents = await collection.find({}).toArray();

    let html = `
        <h1>MongoDB Items</h1>
        <form method="POST" action="/add">
            <input name="item" placeholder="Item" required />
            <input name="qty" placeholder="Quantity" type="number" required />
            <input name="status" placeholder="Status" required />
            <button type="submit">Add</button>
        </form>
        <ul>
    `;

    documents.forEach(doc => {
        html += `
            <li>
                <form method="POST" action="/update/${doc._id}" style="display:inline;">
                    <input name="item" value="${doc.item}" />
                    <input name="qty" type="number" value="${doc.qty}" />
                    <input name="status" value="${doc.status}" />
                    <button type="submit">Update</button>
                </form>
                <form method="POST" action="/delete/${doc._id}" style="display:inline;">
                    <button type="submit">Delete</button>
                </form>
            </li>
        `;
    });

    html += '</ul>';
    res.send(html);
});

// Create
app.post('/add', async (req, res) => {
    const collection = db.collection('documents');
    await collection.insertOne({
        item: req.body.item,
        qty: parseInt(req.body.qty),
        status: req.body.status
    });
    res.redirect('/');
});

// Update
app.post('/update/:id', async (req, res) => {
    const collection = db.collection('documents');
    await collection.updateOne(
        { _id: new ObjectId(req.params.id) },
        { $set: {
            item: req.body.item,
            qty: parseInt(req.body.qty),
            status: req.body.status
        }}
    );
    res.redirect('/');
});

// Delete
app.post('/delete/:id', async (req, res) => {
    const collection = db.collection('documents');
    await collection.deleteOne({ _id: new ObjectId(req.params.id) });
    res.redirect('/');
});

main().catch(console.error);
```

package.json
```json
{
    "name": "node-mongo-app",
    "version": "1.0.0",
    "description": "A simple Node.js app connecting to MongoDB",
    "main": "server.js",
    "scripts": {
        "start": "node server.js",
        "dev": "nodemon server.js"
    },
    "dependencies": {
        "express": "^4.19.2",
        "mongodb": "^6.5.0"
    },
    "devDependencies": {
        "nodemon": "^3.1.10"
    }
}
```

4. **Install nodemon** --> `npm install --save-dev nodemon`
5. **Run the two containers** --> *node-mongo* (mounted) and *mongodb* (data volume).

```bash
docker run -d \
--name node-mongo-app \
--network nodejs-network \
-p 3000:3000 \
-v $(pwd):/usr/src/app \
-w /usr/src/app \
node:latest \
sh -c "npm install && npx nodemon server.js"

docker run -d \
--name mongodb \
--network nodejs-network \
-v mongo-data:/data/db \
-p 27017:27017 \
mongo:latest
```

5. Visit [Localhost:3000](localhost:3000) to see the app.

---
### Subtask 2 — Nginx + Mariadb App
Create two docker containers: Nginx and Mariadb. Create a website served with nginx and displays data from mariadb database.

1. **Create a docker network** --> `docker network create nginx-network`
2. **Project folder** --> `mkdir nginx-mariadb && cd nginx-mariadb`
	1. *Nginx Folder* --> `mkdir nginx`
	2. *PHP Folder* --> `mkdir src`
3. **Create 2 files** --> `touch src/index.php nginx/default.conf`

```php
<!DOCTYPE html>
<html>
<head>
    <title>PHP + Nginx + MariaDB</title>
    <style>
        body {
            font-family: sans-serif;
            background-color: #f4f4f9;
        }
        .container {
            max-width: 800px;
            margin: 40px auto;
            padding: 20px;
            background: white;
            border-radius: 8px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-bottom: 20px;
        }
        th, td {
            padding: 12px;
            border: 1px solid #ccc;
        }
        th {
            background-color: #e2e8f0;
        }
    </style>
</head>
<body>
<div class="container">
    <h1>Inventory CRUD App</h1>

    <?php
    $host = 'mariadb';
    $dbname = 'mydb';
    $user = 'user';
    $pass = 'pass';

    try {
        $pdo = new PDO("mysql:host=$host;dbname=$dbname", $user, $pass);
        $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

        // Create table if not exists
        $pdo->exec("CREATE TABLE IF NOT EXISTS inventory (
            id INT AUTO_INCREMENT PRIMARY KEY,
            product VARCHAR(255),
            quantity INT
        )");

        // Handle Add
        if ($_SERVER['REQUEST_METHOD'] === 'POST' && isset($_POST['add'])) {
            $product = $_POST['product'];
            $qty = $_POST['quantity'];
            $stmt = $pdo->prepare("INSERT INTO inventory (product, quantity) VALUES (?, ?)");
            $stmt->execute([$product, $qty]);
        }

        // Handle Update
        if ($_SERVER['REQUEST_METHOD'] === 'POST' && isset($_POST['update'])) {
            $id = $_POST['id'];
            $product = $_POST['product'];
            $qty = $_POST['quantity'];
            $stmt = $pdo->prepare("UPDATE inventory SET product=?, quantity=? WHERE id=?");
            $stmt->execute([$product, $qty, $id]);
        }

        // Handle Delete
        if ($_SERVER['REQUEST_METHOD'] === 'POST' && isset($_POST['delete'])) {
            $id = $_POST['id'];
            $stmt = $pdo->prepare("DELETE FROM inventory WHERE id=?");
            $stmt->execute([$id]);
        }

        // Fetch all records
        $stmt = $pdo->query("SELECT * FROM inventory");
        $rows = $stmt->fetchAll(PDO::FETCH_ASSOC);

    } catch (PDOException $e) {
        echo "<p style='color:red'>Connection failed: " . $e->getMessage() . "</p>";
    }
    ?>

    <h2>Add New Item</h2>
    <form method="POST">
        <input type="text" name="product" placeholder="Product Name" required>
        <input type="number" name="quantity" placeholder="Quantity" required>
        <button type="submit" name="add">Add</button>
    </form>

    <h2>Inventory List</h2>
    <table>
        <tr><th>ID</th><th>Product</th><th>Quantity</th><th>Actions</th></tr>
        <?php foreach ($rows as $row): ?>
            <tr>
                <form method="POST">
                    <td><?= htmlspecialchars($row['id']) ?></td>
                    <td>
                        <input type="text" name="product" value="<?= htmlspecialchars($row['product']) ?>">
                    </td>
                    <td>
                        <input type="number" name="quantity" value="<?= htmlspecialchars($row['quantity']) ?>">
                    </td>
                    <td>
                        <input type="hidden" name="id" value="<?= $row['id'] ?>">
                        <button type="submit" name="update">Update</button>
                        <button type="submit" name="delete" onclick="return confirm('Are you sure?')">Delete</button>
                    </td>
                </form>
            </tr>
        <?php endforeach; ?>
    </table>
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

4. **Create** Dockerfile for PHP-FPM --> `touch src/Dockerfile`
```dockerfile
# src/Dockerfile
FROM php:7.0-fpm
RUN docker-php-ext-install mysqli pdo pdo_mysql
RUN docker-php-ext-enable pdo
```

5. **Build the image** --> `docker build -t php-fpm-pdo ./src`
6. **Run the three containers** --> *nginx* (config mounted), *mariadb* (data volume) and *php-fpm* (mounted).

```bash
docker run -d \
  --name mariadb \
  --network nginx-network \
  -p 3306:3306 \
  -e MARIADB_ROOT_PASSWORD=rootpass \
  -e MARIADB_DATABASE=mydb \
  -e MARIADB_USER=user \
  -e MARIADB_PASSWORD=pass \
  -v mariadb-data:/var/lib/mysql \
  mariadb:latest

docker run -d \
  --name php-fpm \
  --network nginx-network \
  -v $(pwd)/src:/var/www/html \
  php-fpm-pdo

docker run -d \
  --name nginx-maria \
  --network nginx-network \
  -p 80:80 \
  -v $(pwd)/nginx/default.conf:/etc/nginx/conf.d/default.conf:ro \
  -v $(pwd)/src:/var/www/html:ro \
  nginx:latest
```

---
### Subtask 3 — Configuring Ubuntu Containers via Ansible
Create two Ubuntu containers. Write and push (via SSH) an Ansible playbook from the host machine to configure/install on both containers.

1. **Project Folder** --> `mkdir ubuntu-ansible && cd ubuntu-ansible` 
2. **Create a Dockerfile** --> `touch Dockerfile`

```dockerfile
# Dockerfile
FROM ubuntu:20.04

# Set noninteractive frontend for apt
ENV DEBIAN_FRONTEND=noninteractive

# Install SSH, Python, sudo
RUN apt update && apt install -y \
    openssh-server \
    sudo \
    software-properties-common \
    curl \
    wget

# Install Python 3.10
RUN add-apt-repository ppa:deadsnakes/ppa -y && \
    apt update && \
    apt install -y python3.10 python3.10-distutils && \
    update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.10 1

# Create ansible user with password and sudo privileges
RUN useradd -m -s /bin/bash ansibleuser && \
    echo "ansibleuser:ansible" | chpasswd && \
    usermod -aG sudo ansibleuser && \
    echo "ansibleuser ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# Configure SSH
RUN mkdir -p /var/run/sshd && \
    sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config && \
    sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config && \
    sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin no/' /etc/ssh/sshd_config

# Expose SSH port
EXPOSE 22

# Start SSH on container run
CMD ["/usr/sbin/sshd", "-D"]
```

3. **Build image** --> `docker build -t ubuntu-ansible-ready`
4. **Run 2 Ubuntu containers**
```bash
docker run -itd --name ubuntu-1 -p 2222:22 ubuntu-ansible-ready
docker run -itd --name ubuntu-2 -p 2223:22 ubuntu-ansible-ready
```

WAIT

5. **Install Ansible** 
```bash
sudo apt install software-properties-common -y
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible -y
```

6. **Create `inventory.ini` file
```ini
[ubuntu_containers]
ubuntu-1 ansible_host=127.0.0.1 ansible_port=2222 ansible_user=ansibleuser ansible_ssh_pass=password ansible_become_password=password
ubuntu-2 ansible_host=127.0.0.1 ansible_port=2223 ansible_user=ansibleuser ansible_ssh_pass=password ansible_become_password=password
```

7. **Create an ansible playbook** `configure-ubuntu.yaml`
```yaml
---
- name: Configure Ubuntu Containers
  hosts: ubuntu_containers
  become: yes
  vars:
    nvm_version: "0.39.7"
    node_version: "20"
    nvm_install_dir: "/home/{{ nvm_user }}/.nvm"
    nvm_user: "{{ ansible_user }}"

  tasks:
    - name: Install Python3 and pip (required for Ansible)
      raw: apt update && apt install -y python3 python3-pip

    - name: Update apt cache
      ansible.builtin.apt:
        update_cache: yes
        cache_valid_time: 3600

    - name: Install common prerequisites
      ansible.builtin.apt:
        name:
          - curl
          - build-essential
          - git
        state: present

    - name: Ensure NVM directory exists
      ansible.builtin.file:
        path: "{{ nvm_install_dir }}"
        state: directory
        owner: "{{ nvm_user }}"
        group: "{{ nvm_user }}"
        mode: '0755'

    - name: Download and install NVM
      ansible.builtin.shell: |
        curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v{{ nvm_version }}/install.sh | bash
        export NVM_DIR="{{ nvm_install_dir }}"
        [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
        [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"
      args:
        creates: "{{ nvm_install_dir }}/nvm.sh"
      register: nvm_install_output
      changed_when: nvm_install_output.rc == 0
      become: no
      become_user: "{{ nvm_user }}"

    - name: Add NVM sourcing to .bashrc
      ansible.builtin.lineinfile:
        path: "/home/{{ nvm_user }}/.bashrc"
        line: |
          export NVM_DIR="{{ nvm_install_dir }}"
          [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
          [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"
        insertafter: EOF
        state: present
      become: no
      become_user: "{{ nvm_user }}"

    - name: Install Node.js using NVM
      ansible.builtin.shell: |
        export NVM_DIR="{{ nvm_install_dir }}"
        [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
        nvm install {{ node_version }}
        nvm alias default {{ node_version }}
        nvm use default
      args:
        creates: "{{ nvm_install_dir }}/versions/node/v{{ node_version }}/bin/node"
      register: node_install_output
      changed_when: node_install_output.rc != 0
      become: no
      become_user: "{{ nvm_user }}"

    - name: Install PHP and common extensions
      ansible.builtin.apt:
        name:
          - php
          - php-cli
          - php-fpm
          - php-mysql
          - php-xml
          - php-mbstring
          - php-zip
          - php-gd
        state: present

    - name: Install Apache2
      ansible.builtin.apt:
        name: apache2
        state: present

    - name: Start and enable Apache2
      ansible.builtin.service:
        name: apache2
        state: started
        enabled: yes

    - name: Create a PHP info page
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

    - name: Enable Apache rewrite module
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

9. **Do one of these (for connecting SSH via password)**
  - **Disable ansible host-key checking** by adding this to `.bashrc` file --> `export ANSIBLE_HOST_KEY_CHECKING=False`
  - **Login manually and accept fingerprint**
    - `ssh -p 2222 ansibleuser@127.0.0.1`
    - `ssh -p 2223 ansibleuser@127.0.0.1`
  - **Run inside containers** (passwordless sudo)
    - `echo "ansibleuser ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers`

10. **Run the playbook** --> `ansible-playbook -i inventory.ini configure_ubuntu.yaml`

# Task - Jun 26

- Create 5 Ubuntu containers. 
- Create a shell script that SSHs into all of them and gets their system information into a log file
- A cronjob runs every hour that emails it to abdullah.kaleem@jumppace.com.

1. 