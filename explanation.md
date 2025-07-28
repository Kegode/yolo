

 ## 1. Selection of the foundation image against which a given container may be built.
Frontend: applied `node:18-alpine` due to it being  lightweight and secure.
Backend: The node:18-alpine was the choice since it is the latest LTS-based lightweight and secure image.
MongoDB:`mongo:6.0` image is stable as it already exists in docker hub.

 ## 2. Dockerfile commands that were used to create and run every container.

The working directory is set to `/app/* immediatly below `test_file` by setting `WORKDIR`.
COPY copies its dependencies to ./app first because this improves caching.
Packages are installed using the command `RUN npm install`.
The frontend port is accessible by `EXPOSE 5000`.
The backend port is exposed by `EXPOSE 5000`.
The app is run by the command `CMD ["npm", "start"]`.

 ## 3. Docker Compose Networking

Applied a special bridge network (`ecommerce-network`) to perform service-to-service communication.
Specified ports per service in order to not conflict and permit local testing.

 ## 4. Creation of a volume and use of docker-compose 

Specified `my-products` volume as data persistence even through restarting of containers.

 ## 5 Debugging

viewed logs using `docker logs`.
Reset environment by `docker-compose down -v` and `docker-compose up --build`.
Ran `docker ps` and `docker exec` to make sure the services are healthy.


 ## 6. Deployed docker image screenshot

![Screenshot](frontend.png)
![Screenshot](backend.png)

# ANSIBLE AND VAGRANT


### 🧩 Playbook Structure

```yaml
- hosts: all
  become: true
  roles:
    - install_docker
    - clone_app
    - run_app
```

The playbook above runs **three roles sequentially**, from top to bottom. This order is intentional and critical for correct system provisioning and application deployment.

---

## 🔧 Roles Breakdown

---

### 1. ✅ `install_docker`

**Purpose:**  
Install Docker Engine, Docker CLI, and Docker Compose, and configure the system to run containers.

**Why it's first:**  
Docker must be installed before attempting to run or build any containers. It is the base on which the entire app stack runs.

**Key Tasks and Modules:**

| Task | Module | Description |
|------|--------|-------------|
| Install dependencies | `apt` | Installs base packages like `curl`, `gnupg`, `software-properties-common` required to set up Docker. |
| Add Docker GPG key | `apt_key` | Adds Docker’s official GPG key to verify Docker packages. |
| Add Docker repository | `apt_repository` | Adds the Docker APT repo for Ubuntu. |
| Install Docker engine | `apt` | Installs `docker-ce`, `docker-ce-cli`, and `containerd.io`. |
| Add Docker group permissions | `shell` | Adds the `vagrant` user to the Docker group. |
| Install pip3 | `apt` | Ensures Python’s package manager is available. |
| Install Docker SDK via pip | `pip` | Installs Python Docker SDK and `docker-compose` to allow further Docker automation. |

---

### 2. 📥 `clone_app`

**Purpose:**  
Clone the application repository from GitHub into the local machine.

**Why it's second:**  
The application source code must be present **before** we can run Docker Compose on it.

**Key Tasks and Modules:**

| Task | Module | Description |
|------|--------|-------------|
| Clone project | `git` | Clones the project from GitHub into `/home/vagrant/yolo`. Uses the `master` branch. The `force: yes` option ensures any existing repo is overwritten. |

---

### 3. 🚀 `run_app`

**Purpose:**  
Build and run the Dockerized app using `docker-compose`.

**Why it's last:**  
This role **requires Docker** to be installed (from Role 1) and **application files** to be available (from Role 2). It brings up the full application stack.

**Key Tasks and Modules:**

| Task | Module | Description |
|------|--------|-------------|
| Run Docker Compose | `command` | Executes `docker compose up -d --build` to build images and start services in detached mode. Uses `chdir` to ensure the command runs from the correct directory. |

---

## 🔁 Automation Flow Summary

The roles are ordered to respect dependencies:

1. 🛠️ **install_docker**: Ensures Docker is set up properly.
2. 📁 **clone_app**: Pulls in your app’s source code.
3. 📦 **run_app**: Uses Docker Compose to bring the app to life.

This guarantees a smooth and fully automated infrastructure setup.

---

## ✅ Best Practices Observed

- ✅ Use of **roles** to modularize tasks.
- ✅ Use of **official Ansible modules** (`apt`, `git`, `command`, `pip`, etc.) for idempotent actions.
- ✅ Proper use of **variables**, **become**, and **chdir**.
- ✅ Logical **execution order** matching dependency hierarchy.
