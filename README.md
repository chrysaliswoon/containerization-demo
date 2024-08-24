# Containerization Demo

The goal of this activity is to have your node application containerised onto a self-built node baseimage and have it deployed on a remote host (i.e. an EC2).

## Setup Docker on EC2

First start with setting up the remote host and validating that we can host and access a simple web server (e.g. nginx)  on it.

### Step 1: Create an EC2 Instance

#### 1.1. **Launch an EC2 Instance**

1. **Log in to AWS Management Console:**
   - Go to [AWS Management Console](https://aws.amazon.com/console/).

2. **Navigate to EC2 Dashboard:**
   - Select "EC2" from the list of services.

3. **Launch a New Instance:**
   - Click **Launch Instance**.

4. **Choose an Amazon Machine Image (AMI):**
   - Select an appropriate AMI. For this tutorial, Amazon Linux 2 is a good choice.

5. **Choose an Instance Type:**
   - Select `t2.micro` (eligible for the free tier).

6. **Configure Instance Details:**
   - Leave the default settings or adjust as needed for your use case.

7. **Add Storage:**
   - Default storage is usually sufficient.

8. **Add Tags (optional):**
   - Add tags if you need to identify the instance later.

9. **Configure Security Group:**
   - **Create a new security group or modify an existing one**.
   - Add the following inbound rules:
     - **SSH (Port 22)**: To connect via SSH. Source: `0.0.0.0/0` (or restrict to your IP for security).
     - **Custom TCP Rule (Port 8080)**: To allow access to the Nginx server. Source: `0.0.0.0/0` (or restrict to your IP).

10. **Review and Launch:**
    - Review your settings and click **Launch**.
    - Select or create a key pair to access the instance.

#### 1.2. **Obtain the Public IP Address:**

   - After the instance is running, find the public IP address from the EC2 dashboard.

### Step 2: Connect to the EC2 Instance

#### 2.1. **SSH into the EC2 Instance**

1. **Open your terminal (Command Prompt or Terminal).**

2. **Connect using SSH:**
   - Replace `/path/to/your-key.pem` with the path to your key file and `<your-ec2-public-ip>` with the instance's public IP:
     ```bash
     ssh -i /path/to/your-key.pem ec2-user@<your-ec2-public-ip>
     ```

### Step 3: Install Docker

#### 3.1. **Update and Install Docker**

1. **Update the package index:**
   ```bash
   sudo yum update -y
   ```

2. **Install Docker:**
   ```bash
   sudo dnf install -y docker
   ```

3. **Start Docker Service:**
   ```bash
   sudo service docker start
   ```

4. **Add User to Docker Group (optional but recommended):**
   ```bash
   sudo usermod -aG docker ec2-user
   ```

   - Log out and log back in to apply the group change, or run `newgrp docker`.

5. **Verify Docker Installation:**
   ```bash
   docker --version
   ```

### Step 4: Run Nginx in Docker

#### 4.1. **Run Nginx Container**

1. **Run the Nginx container:**
   ```bash
   docker run --rm -dp 8080:80 nginx
   ```

   - This command will pull the `nginx` image from Docker Hub if it’s not already downloaded, start a container mapping port 80 in the container to port 8080 on the host, and remove the container when stopped.

### Step 5: Verify Access

#### 5.1. **Access the Nginx Server**

1. **Open a web browser.**

2. **Navigate to the public IP of your EC2 instance on port 8080:**
   ```http
   http://<your-ec2-public-ip>:8080
   ```

   - Replace `<your-ec2-public-ip>` with the actual IP address of your EC2 instance.

3. **Verify that you see the default Nginx welcome page.**

### Summary

1. **Create EC2 Instance**: Launch with security groups allowing ports 22 and 8080.
2. **Connect**: SSH into your EC2 instance.
3. **Install Docker**: Update system, install Docker, start service.
4. **Run Nginx**: Use Docker to run Nginx and map it to port 8080.
5. **Verify Access**: Open your browser and access the instance to ensure Nginx is running.

This process will set up an EC2 instance with Docker and Nginx running, accessible via the specified ports. 

----------------------------------------------------------------------------------------------------------------------------------------------

Here's a step-by-step guide for building your own Docker image with Node.js installed on a slim Debian base:

### 1. **Visit Docker Hub and Find the Latest Slim Version of Debian**

1. **Go to Docker Hub:**
   - Visit [Docker Hub](https://hub.docker.com/).

2. **Search for the Latest Slim Debian Image:**
   - Search for `debian` and look for the slim variant.
   - For instance, you might find `debian:slim` or a similar tag.

### 2. **Run the Debian Slim Image as a Container and Exec Into It**

1. **Pull and Run the Debian Slim Image:**
   - Run a container from the Debian slim image:

     ```bash
     docker run -d debian:slim sleep 60
     ```

   - or directly start an interactive container session:

     ```bash
     docker run -it debian:bookworm-slim bash
     ```

2. **Exec Into the Running Container (Option 1):**
   - If using the detached mode (first command), find the container ID:

     ```bash
     docker ps
     ```

   - Exec into the container:

     ```bash
     docker exec -it <container_id> bash
     ```

### 3. **Navigate to the Node.js Installation Documentation**

1. **Visit the Node.js Official Documentation:**
   - Go to the [Node.js package manager installation documentation](https://nodejs.org/en/download/package-manager/all).

2. **Find Installation Instructions:**
   - Locate the instructions for installing Node.js on Debian-based systems.

3. **Select Node.js Version:**
   - Pick the version that matches the major version of Node.js installed on your local machine.

### 4. **Install Node.js in the Container**

1. **Follow Installation Steps:**
   - Based on the documentation, execute the necessary commands to install Node.js. Typically, these steps involve adding the Node.js repository and installing Node.js using `apt-get`.

   Example commands to install Node.js on Debian might include:
    
    **Before you begin, ensure that curl is installed on your system. If curl is not installed, you can install it using the following command:**
    ``` bash
    sudo apt-get install -y curl
    ```

    **Download the setup script:**
   ```bash
   curl -fsSL https://deb.nodesource.com/setup_22.x -o nodesource_setup.sh
   ```

   **Run the setup script with sudo:**
   ```bash
   sudo -E bash nodesource_setup.sh
   ```

   **Install Node.js:**
   ```bash
   sudo apt-get install -y nodejs
   ```

   **Verify the installation:**
   ```bash
   node -v
   ```

   Replace `<node_version>` with the appropriate version number (e.g., `18` for Node.js 18).

   Note:
   - Ensure that you have curl installed:

   ```bash
   curl --version
   ```

   If you don't have curl installed, then install curl

   ```bash
   apt-get update
   ```

   ```bash
   apt-get install -y curl
   ```



2. **Verify Installation:**

   ```bash
   node --version
   npm --version
   ```

   Ensure that the versions match your expectations.

3. **Take Note of Essential Commands:**
   - Document the commands needed to install Node.js for later use in your Dockerfile.

### 5. **Create Your Dockerfile**

1. **Create a New Project Directory:**

   ```bash
   mkdir my-node-image
   cd my-node-image
   ```

2. **Create a Dockerfile in your local repo:**

   ```bash
   touch Dockerfile
   ```

3. **Edit the Dockerfile:**

   Open the `Dockerfile` nd add the following content, using the commands you used to install Node.js

### 6. **Build the Docker Image**

1. **Build the Image:**

   ```bash
   docker build -t <image>:<tag> .
   ```

   Replace `<tag>` with a descriptive tag, such as `23-slim` if you're using Node.js 23.

### 7. **Run the Newly Built Image and Verify Installation**

1. **Run a Container from the Newly Built Image:**

   ```bash
   docker run -it <image>:<tag> bash
   ```

   Replace `<tag>` with the tag you used during the build.

2. **Verify Node.js Installation in the Container:**

   ```bash
   node --version
   npm --version
   ```

   Ensure that Node.js and npm are correctly installed and that the versions match your requirements.

### Summary

1. **Find and Run Debian Slim Image:** Use Docker Hub to find the Debian slim image and run it.
2. **Install Node.js:** Follow the installation steps from the Node.js documentation.
3. **Create Dockerfile:** Document installation commands in a Dockerfile.
4. **Build Docker Image:** Build the image with Node.js installed.
5. **Verify Installation:** Run and verify the Node.js installation in the container.

This process allows you to create a custom Docker image with Node.js installed, tailored to your specific needs.