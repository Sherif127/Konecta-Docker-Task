# 🧪 Part 1: Docker Images — Theory

1. **What is the difference between an image and a container?**  
   - **Image**: Immutable, just a template (snapshot) ready to run.  
   - **Container**: A running instance of an image, has state and can be changed.  

---

2. **What happens if you run `docker run nginx` twice without removing the first container?**  
   - The first one runs normally.  
   - The second will fail if the same port (e.g., 80) is already in use.  
   - You can run them both, but you must map different host ports.

---

3. **Can two containers be created from the same image at the same time?**  
   - Yes. Each one has its own **isolated filesystem** (copy-on-write).  
   - Changes inside one container do not affect the others.

---

4. **What’s the difference between `docker image ls` and `docker ps`?**  
   - `docker image ls`: Lists images stored locally.  
   - `docker ps`: Lists running containers.

---

5. **What’s the purpose of tagging an image (e.g., `myapp:1.0`)?**  
   - To differentiate between versions (`myapp:1.0` vs `myapp:2.0`).  
   - If no tag is specified, Docker defaults to `:latest`.

---

6. **How does Docker know which image to use when you run `docker run ubuntu`?**  
   - It looks locally first.  
   - If not found, it pulls from Docker Hub (the default registry).

---

7. **If you delete a container, does it delete the image too? Why or why not?**  
   - No. The image stays.  
   - Images are independent, and a container is only an instance of it.

---

8. **What does this command do?**
   ```bash
   docker pull ubuntu && docker run -it ubuntu
   ```
   - Pulls the Ubuntu image.  
   - Starts it in interactive terminal mode.

---

9. **You have a local image `nginx:latest`. What happens if you run `docker pull nginx` again?**  
   - Docker compares the digest.  
   - If it’s the same version → “Image is up to date.”  
   - If updated → downloads the new one.

---

10. **What’s the difference between these two commands:**
    ```bash
    docker rmi nginx
    docker image prune
    ```
    - `docker rmi nginx`: Removes a specific image.  
    - `docker image prune`: Removes untagged / unused images.

---

11. **True or False: Docker images can be shared between different operating systems.**  
   - **Partially False**:  
     - Images work across the same architecture (x86, ARM).  
     - They won’t run on a different architecture unless it’s a multi-arch image.

---

12. **Can you save a Docker image as a file and share it without pushing it to Docker Hub? How?**  
   - Yes, using `docker save` and `docker load`.

---

13. **What is the result of this command? Why might you use it?**
    ```bash
    docker save -o backup.tar nginx
    ```
   - Saves the `nginx` image to `backup.tar`.  
   - Useful for backup or transferring between machines.

---

14. **How can you copy an image from one machine to another without using Docker Hub or a registry?**  
   - Use `docker save` → copy the file → `docker load`.

---

15. **How do you inspect the internal metadata of an image? What kind of information can you find?**  
   - Use `docker inspect <image>`.  
   - You can see: environment variables, entrypoint, layers, volumes, exposed ports, and OS details.

---

# 🌐 Part 2: Networking and Bridge Mode — Theory

4. **Run two containers without specifying a network:**  
   - Both connect by default to the **`docker0` bridge network**.

---

5. **Try to ping `container1` from `container2`: What happens? Why?**  
   - It fails.  
   - Because the default bridge does not support automatic hostname resolution.

---

6. **Inspect the `docker0` bridge network and check container IPs:**  
   - Shows the bridge information, IP range, and containers’ assigned IP addresses.

---

7. **Now try pinging `container1` from `container2` using IP address.**  
   - It works.  
   - Containers in the same bridge can communicate directly using their IPs.

---

# 🌍 Part 3: Port Forwarding — Theory

8. **Run an Nginx container with port forwarding:**  
   ```bash
   docker run -d -p 8080:80 nginx
   ```
   - Maps port 80 in the container to port 8080 on the host.

---

9. **Access the container from the browser or using `curl`:**  
   - Open `http://localhost:8080` or run `curl http://localhost:8080`.  
   - You’ll see the default Nginx welcome page.

---

10. **Try running a second Nginx container with the same port mapping. What happens? Why?**  
   - It fails with a port binding error.  
   - Because only one process can bind to the same host port at a time.  
   - You must map to a different port (e.g., `-p 8081:80`).
---

# 🐳 Docker Volumes & Containerization Task

## Objective
The goal of this task is to practice:

- Writing a simple Dockerfile (Alpine + curl).
- Using 3 types of Docker volumes (Bind Mount, Named Volume, Anonymous Volume).
- Containerizing a small Node.js/TypeScript application.

---

## Part 1 – Basic Dockerfile

### Steps
1. Created a folder named `docker_task`.
2. Wrote a Dockerfile 

```Dockerfile
FROM alpine 
RUN apk add curl
CMD ["echo", "hello from container"]
```
3. Built the image and named it:

   ```bash
   docker build -t my-basic-image:v1 .
   ```

4. Ran the container:

   ```bash
   docker run my-basic-image:v1
   ```

📸 **Screenshot:**  
![Basic Dockerfile](https://github.com/user-attachments/assets/bde8296e-b9f3-4aa5-aa50-53a89afb5e43)

---

## Part 2 – Docker Volumes

### 1️⃣ Bind Mount
- Created a folder `data_bind` with a file `bind_note.txt`.
- Mounted the local folder into the container path `/app/data`:

  ```bash
  docker run -it --rm     --volume $(pwd)/data_bind:/app/data     my-basic-image:v1 sh
  ```

- Inside the container, verified that `bind_note.txt` exists.

📸 **Screenshot:**  
![Bind Mount](https://github.com/user-attachments/assets/eb8431f0-63a8-4e61-87cb-7c9323eae026)

---

### 2️⃣ Named Volume
- Created a Docker named volume:

  ```bash
  docker volume create my_named_volume
  ```

- Ran the container with the named volume:

  ```bash
  docker run -it --rm     --volume my_named_volume:/app/named     my-basic-image:v1 sh
  ```

- Created a file `/app/named/note.txt` inside the container.
- After exiting and re-running, the file persisted ✅.

📸 **Screenshot:**  
![Named Volume](https://github.com/user-attachments/assets/254df1b6-eb32-4a9e-9d72-0cb63298a8b8)

---

### 3️⃣ Anonymous Volume
- Ran the container with an anonymous volume mounted on `/app/anon`:

  ```bash
  docker run -it --rm     --volume /app/anon     my-basic-image:v1 sh
  ```

- Created a file `anon.txt` inside `/app/anon`.
- After running:

  ```bash
  docker volume ls
  ```

  Found a new volume without a Strange name.

📸 **Screenshots:**  
Run with Anonymous Volume:  
![Run with Anonymous Volume](https://github.com/user-attachments/assets/c50755c9-9690-4059-8c30-875de4413ff6)  

Check with `docker volume ls`:  
![Volume List](https://github.com/user-attachments/assets/d09c39ad-f93a-480f-97a7-635d2954c4cf)

---

## Part 3 – Node.js/TypeScript App Containerization

### Application Files
- `package.json`: includes scripts (`build`, `start`) and dependencies (express).
- `tsconfig.json`: TypeScript configuration.
- `src/index.ts`: simple Express server with endpoints `/health` and `/whoami`.

### Dockerfile
```dockerfile
FROM node:20
WORKDIR /app
COPY . .
RUN npm install
RUN npm run build
EXPOSE 3000
CMD ["npm", "run", "start"]
```

### Build & Run
1. Build the image:

   ```bash
   docker build -t testimage .
   ```

- Build image:  
  ![Final Task Build Image](https://github.com/user-attachments/assets/ea7b783e-9481-482a-bb16-8f6c6a3970c9")

2. Run the container:

   ```bash
   docker run -d -p 3000:3000 testimage
   ```

3. Test with curl:

   ```bash
   curl http://localhost:3000/health
   ```

- Run container & curl health check:  
  ![Final Task Run and Curl](https://github.com/user-attachments/assets/b0e1f889-4faf-4224-bc9c-d76af56e7f1f)

4. Open in browser:
 
  ![Final Task Browser](https://github.com/user-attachments/assets/05080dd6-3dd6-40d2-bb5b-e5abe3934a89)

---

## ✅ What Was Achieved
- Wrote a basic Dockerfile (Alpine + curl + CMD).
- Tested 3 types of Docker Volumes:
  - **Bind Mount**: sharing folder from host system.
  - **Named Volume**: persistent storage.
  - **Anonymous Volume**: temporary storage without a name.
- Containerized a Node.js/TypeScript application.
- Ran it on port **3000** and verified via curl & browser.
