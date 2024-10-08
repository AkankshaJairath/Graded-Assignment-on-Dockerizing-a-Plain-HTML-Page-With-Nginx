# Graded-Assignment-on-Dockerizing-a-Plain-HTML-Page-With-Nginx

# Dockerizing a Simple HTML Page with Nginx

This project demonstrates how to Dockerize a simple HTML page using **Nginx** as the web server, push the Docker image to **AWS ECR**, and deploy the container on **AWS ECS** (Elastic Container Service).

## Project Overview

This repository contains a basic Docker project that serves a static HTML page using Nginx. The workflow includes:

- Creating a simple HTML page (`index.html`)
- Configuring Nginx to serve the page (`nginx.conf`)
- Writing a `Dockerfile` to containerize the Nginx setup
- Pushing the image to **AWS ECR** (Elastic Container Registry)
- Deploying the Docker container using **AWS ECS**

## Prerequisites

Before starting, make sure you have the following set up:

- An **AWS account**
- **AWS CLI** installed and configured
- **Docker** installed on your local machine

### Dependencies

- **Nginx** (used as the web server)
- **Docker** (for containerization)
- **AWS CLI** (for interaction with AWS services)
- **AWS ECS and ECR** (for container orchestration and image storage)

## Steps

### 1. Create an EC2 Instance and Install Docker & AWS CLI

- Launch an EC2 instance on AWS
- Install Docker and AWS CLI on the instance:

  ```bash
  sudo yum install docker -y
  curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
  unzip awscliv2.zip
  sudo ./aws/install
  aws --version
  aws configure    # Configure the AWS CLI by creating a token using IAM
  ```

- Ensure Docker is running:

  ```bash
  sudo service docker start
  ```

### 2. Create Basic HTML Page (`index.html`)

Create a simple `index.html` file with the following content:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Hello, Docker!</title>
</head>
<body>
    <h1>Welcome to Dockerized Nginx!</h1>
</body>
</html>
```

### 3. Create Nginx Configuration (`nginx.conf`)

Configure Nginx to serve the `index.html` page by creating the following `nginx.conf` file:

```nginx
server {
    listen 80;
    server_name localhost;

    location / {
        root /usr/share/nginx/html;
        index index.html;
    }
}
```

### 4. Create the Dockerfile

The Dockerfile defines the image that will be built. Here is an example Dockerfile:

```Dockerfile
# Use the official Nginx base image
FROM nginx:alpine

# Copy the custom Nginx configuration file
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Copy the HTML page
COPY index.html /usr/share/nginx/html/index.html

# Expose port 80
EXPOSE 80

# Start Nginx when the container starts
CMD ["nginx", "-g", "daemon off;"]
```

### 5. Build and Push the Docker Image to Docker Hub or AWS ECR

#### Build the Docker Image

To build the image, run the following command in your terminal:

```bash
docker build -t my-nginx-app .
```

#### Push to Docker Hub (Optional)

Login to Docker Hub and push the image:

```bash
docker login
docker tag my-nginx-app bharatbhushan05/my-nginx-app:latest
docker push bharatbhushan05/my-nginx-app:latest
```

#### Push to AWS ECR

1. **Create an ECR repository** in AWS.

2. **Login to ECR** from your EC2 instance:

   ```bash
   aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 975050024946.dkr.ecr.us-west-2.amazonaws.com
   ```

3. **Tag and Push the Image** to ECR:

   ```bash
   docker tag my-nginx-app:latest 975050024946.dkr.ecr.us-west-2.amazonaws.com/bharat/nginx:latest
   docker push 975050024946.dkr.ecr.us-west-2.amazonaws.com/bharat/nginx:latest
   ```

### 6. Deploy the Container Using AWS ECS

1. **Create a Cluster in AWS ECS**:
   - Use the AWS Management Console to create a cluster.

2. **Create a Task Definition**:
   - Define your task to use the image you pushed to ECR.

3. **Create a Service**:
   - Deploy the task to your ECS cluster by creating a service.

4. **Access the Service**:
   - Attach a load balancer to the service if necessary and access the Nginx page via the Load Balancer DNS.

### 7. Verify the Deployment

Once deployed, you can access the application via the Load Balancer DNS (if an ALB is used) or directly through the public IP address of the ECS service.

### Diagram

For a detailed diagram flow of the process, refer to the following link:

- [Diagram-Flow](https://app.eraser.io/workspace/H2FgUXbU9GDuCkIien6E?origin=share)

## Conclusion

This project demonstrates how to create a simple Nginx-based Docker application, push it to AWS ECR, and deploy it via AWS ECS.

## Resources

- [GitHub Repository](https://github.com/devops-bharat05/Dockerizing-HTML)
- [AWS ECR Documentation](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html)
- [AWS ECS Documentation](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html)
