# 🚀 Real-Time Collaborative Editor — Docker & AWS ECS Deployment

A real-time collaborative editor built with a full-stack JavaScript architecture and deployed as a containerized application on **Amazon Web Services (AWS)**.

This project was developed primarily as a hands-on learning project to understand how a real-world application moves from:

**Local Development → Production Build → Docker Container → Amazon ECR → Amazon ECS → Load Balancer → Public Deployment**

Along with cloud deployment, the application explores real-time collaboration using **Socket.IO** and conflict-resolution concepts using **CRDTs (Conflict-Free Replicated Data Types)**.

---

# 📌 Table of Contents

- [Project Overview](#-project-overview)
- [Why I Built This Project](#-why-i-built-this-project)
- [Main Features](#-main-features)
- [Technology Stack](#-technology-stack)
- [Project Architecture](#-project-architecture)
- [Real-Time Collaboration](#-real-time-collaboration)
- [Socket.IO](#-socketio)
- [CRDT](#-crdt)
- [Frontend](#-frontend)
- [Backend](#-backend)
- [Production Build](#-production-build)
- [Docker](#-docker)
- [Docker Image](#-docker-image)
- [Dockerfile](#-dockerfile)
- [Docker Build Process](#-docker-build-process)
- [AWS Architecture](#️-aws-architecture)
- [AWS IAM](#-aws-iam)
- [Amazon ECR](#-amazon-ecr)
- [Amazon ECS](#-amazon-ecs)
- [ECS Cluster](#-ecs-cluster)
- [Task Definition](#-task-definition)
- [ECS Service](#-ecs-service)
- [Application Load Balancer](#-application-load-balancer)
- [Target Groups](#-target-groups)
- [Health Checks](#-health-checks)
- [Monitoring](#-monitoring)
- [Deployment Flow](#-complete-deployment-flow)
- [Challenges](#-challenges-and-learning)
- [Cost Management](#-aws-cost-management)
- [Security](#-security-considerations)
- [What I Learned](#-what-i-learned)
- [Future Improvements](#-future-improvements)
- [Project Status](#-project-status)

---

# 📖 Project Overview

The **Real-Time Collaborative Editor** is a web application where multiple users can work on shared content in real time.

The project helped me explore two major areas of software development:

### Application Development

Understanding how real-time applications communicate and synchronize changes between multiple connected users.

### Cloud & DevOps

Understanding how a full-stack application can be packaged using Docker and deployed on AWS using container services.

The cloud deployment part became one of the main learning objectives of this project.

---

# 🎯 Why I Built This Project

Building an application locally is only one part of software development.

I wanted to understand what happens after development is complete.

Questions I wanted to answer included:

- How do we package an application?
- What exactly is a Docker image?
- Why do we need containers?
- How can a frontend production build be served?
- Where do Docker images get stored?
- How does AWS run Docker containers?
- What is an ECS cluster?
- What is a task definition?
- What is an ECS service?
- How does a load balancer communicate with containers?
- How are health checks performed?
- How do IAM permissions control AWS resources?
- How can we remove resources after testing to prevent unnecessary billing?

This project gave me practical exposure to these concepts.

---

# ✨ Main Features

The application explores the following features:

- Real-time collaborative editing
- Multiple connected users
- Real-time communication
- Socket-based updates
- Conflict-resolution concepts
- CRDT-based synchronization concepts
- Node.js backend
- Frontend production build
- Docker containerization
- AWS container deployment
- ECS service management
- Load balancing
- Health monitoring

---

# 🛠 Technology Stack

## Frontend

- JavaScript
- React
- HTML
- CSS

## Backend

- Node.js
- Express.js
- Socket.IO

## Real-Time System

- WebSockets through Socket.IO
- CRDT concepts

## Containerization

- Docker
- Dockerfile
- `.dockerignore`

## AWS

- AWS IAM
- Amazon ECR
- Amazon ECS
- ECS Cluster
- ECS Task Definitions
- ECS Services
- Application Load Balancer
- Target Groups
- Health Checks
- CloudWatch monitoring

## Development Tools

- Git
- GitHub
- VS Code
- npm

---

# 🏗 Project Architecture

At a high level, the application consists of:

```text
                User A
                  │
                  │
                  ▼
             ┌─────────┐
             │ Browser │
             └────┬────┘
                  │
                  │
                  ▼
           ┌───────────────┐
           │   Frontend    │
           │     React     │
           └───────┬───────┘
                   │
                   │
             HTTP / Socket.IO
                   │
                   ▼
           ┌───────────────┐
           │    Backend    │
           │ Node + Express│
           │   Socket.IO   │
           └───────┬───────┘
                   │
                   │
            Synchronization
                   │
                   ▼
             Other Users
```

Multiple users can communicate with the server, and real-time events can be distributed to connected clients.

---

# ⚡ Real-Time Collaboration

Traditional web applications often work using a request-response model.

For example:

```text
Client
   │
   │ HTTP Request
   ▼
Server
   │
   │ HTTP Response
   ▼
Client
```

For a collaborative editor, users need updates much faster.

If User A changes shared content, User B should receive that update without manually refreshing the page.

The desired behavior is:

```text
User A
   │
   │ Change
   ▼
Server
   │
   ├──────────────► User B
   │
   └──────────────► User C
```

This is where real-time communication becomes useful.

---

# 🔌 Socket.IO

Socket.IO was used to explore real-time communication between the backend and connected clients.

Instead of repeatedly requesting the server:

```text
Client → Request → Server
Client → Request → Server
Client → Request → Server
```

a persistent connection can be used:

```text
Client
   │
   │ Persistent Connection
   │
Server
   │
   │ Event
   ▼
Client
```

This allows events to be delivered quickly between connected users.

---

# 🔄 CRDT

Collaborative systems introduce another challenge:

> What happens if multiple users edit the same shared data at nearly the same time?

For example:

```text
Original:

Hello World

User A:
Hello AWS World

User B:
Hello Docker World
```

Both users may be modifying their own local version.

A collaborative system needs a strategy to synchronize these changes.

This project explores **CRDT — Conflict-Free Replicated Data Type** concepts.

CRDTs are designed for distributed systems where multiple replicas may update data independently.

Conceptually:

```text
Replica A ─────┐
               │
               ▼
          Synchronization
               ▲
               │
Replica B ─────┘
```

The goal is for replicas to eventually reach a consistent state without requiring every operation to happen on one machine at exactly the same moment.

This project helped me understand why conflict resolution is an important problem in collaborative and distributed applications.

---

# 💻 Frontend

The frontend provides the user-facing interface.

During development, the frontend runs using its development environment.

For production deployment, however, we don't normally deploy the development server directly.

Instead, we generate a production build.

---

# 🏭 Production Build

The frontend is converted into optimized production files using:

```bash
npm run build
```

Conceptually:

```text
React Source Code
       │
       │ npm run build
       ▼
Production Build
       │
       ├── HTML
       ├── CSS
       └── JavaScript
```

The generated frontend files can then be served through the backend.

This allowed the application to be packaged together for deployment.

---

# 🖥 Backend

The backend uses Node.js and Express.

Its responsibilities include handling server-side application logic and supporting real-time communication through Socket.IO.

Conceptually:

```text
Frontend
    │
    │ Request / Socket Event
    ▼
Node.js + Express
    │
    │
    ▼
Application Logic
```

The backend also provides the environment from which the production frontend can be served.

---

# 🐳 Docker

One of the biggest learning areas of this project was Docker.

Before using Docker, an application depends on the environment where it runs.

It may require:

```text
Application Code
Node.js
npm packages
Runtime configuration
Operating-system environment
```

Different environments can sometimes create compatibility problems.

Docker helps package the application and its runtime requirements into a reproducible container image.

---

# 📦 Docker Image

A simplified way I understand a Docker image is:

```text
Application Code
       +
Dependencies
       +
Runtime
       +
Required Environment
       │
       ▼
   Docker Image
```

That image can then be used to start containers.

```text
Docker Image
     │
     ├────► Container 1
     ├────► Container 2
     └────► Container 3
```

An image is the packaged template.

A container is a running instance created from that image.

---

# 📄 Dockerfile

The Dockerfile defines the steps Docker should follow to build the application image.

During this project, I worked with Dockerfile instructions such as:

```dockerfile
FROM
WORKDIR
COPY
RUN
EXPOSE
CMD
```

### `FROM`

Defines the base image.

For a Node.js application, this is generally a Node.js runtime image.

### `WORKDIR`

Defines the working directory inside the container.

### `COPY`

Copies application files into the image.

### `RUN`

Runs commands during the image-building process.

For example, dependencies may be installed during this stage.

### `EXPOSE`

Documents the port used by the application.

### `CMD`

Defines the command that starts the application when the container runs.

---

# 🔨 Docker Build Process

The basic process I learned was:

```text
Application
     │
     ▼
Dockerfile
     │
     │ docker build
     ▼
Docker Image
     │
     │ docker run
     ▼
Container
```

The image contains everything necessary to run the application in the expected environment.

---

# 🧱 Frontend + Backend Containerization

The project contains both frontend and backend code.

The frontend first needs to be converted into a production build.

```text
Frontend Source
      │
      │ npm run build
      ▼
Frontend Production Files
```

Those files are then made available through the backend.

Conceptually:

```text
Frontend
   │
   │ Build
   ▼
Static Production Files
   │
   ▼
Backend Public Files
   │
   ▼
Node.js Server
```

The application can then be packaged using Docker.

---

# ☁️ AWS Architecture

After understanding Docker locally, the next goal was to run the container in AWS.

The important distinction I learned is:

**ECR stores Docker images.**

**ECS runs containers created from those images.**

The architecture was approximately:

```text
Local Application
       │
       ▼
   Docker Build
       │
       ▼
   Docker Image
       │
       ▼
   Amazon ECR
       │
       ▼
   Amazon ECS
       │
       ▼
 ECS Task Definition
       │
       ▼
   ECS Service
       │
       ▼
Application Load Balancer
       │
       ▼
     Users
```

---

# 🔐 AWS IAM

Before deploying AWS resources, permissions need to be configured.

AWS IAM — Identity and Access Management — controls who can access AWS services and what actions they are allowed to perform.

I created an IAM user for working with AWS instead of performing development operations using the root account.

During the project, permissions related to services such as ECR and ECS were configured.

This helped me understand:

```text
IAM User
   │
   ▼
IAM Policies
   │
   ▼
Allowed AWS Actions
```

IAM became an important part of understanding AWS security.

---

# 📦 Amazon ECR

After creating the Docker image, it needs to be stored somewhere that AWS can access.

For this, I worked with **Amazon Elastic Container Registry (ECR)**.

ECR acts as a container image registry.

The flow is:

```text
Local Machine
     │
     │ Build
     ▼
Docker Image
     │
     │ Push
     ▼
Amazon ECR
```

One important correction I learned during this process was:

> We don't upload the Docker image directly to ECS.

Instead:

```text
Docker Image
      │
      ▼
     ECR
      │
      │ ECS pulls image
      ▼
     ECS
```

---

# 🚢 Amazon ECS

Amazon Elastic Container Service was used to run the containerized application.

ECS manages containers running in AWS.

While learning ECS, I worked with several important concepts:

```text
ECS
├── Cluster
├── Task Definition
├── Task
└── Service
```

Initially these concepts looked similar, but deploying the application helped me understand their individual purposes.

---

# 🗂 ECS Cluster

An ECS cluster is the logical environment where ECS workloads are organized.

Conceptually:

```text
ECS Cluster
     │
     ├── Service
     │     │
     │     └── Tasks
     │
     └── Other Services
```

I created a cluster and used it as the environment for running the application.

---

# 📋 Task Definition

A Task Definition acts like a blueprint describing how ECS should run the container.

It can contain configuration such as:

```text
Docker Image
CPU
Memory
Container Port
Environment Configuration
Runtime Settings
```

One useful mental model is:

```text
Dockerfile
   │
   │ Builds
   ▼
Docker Image

Task Definition
   │
   │ Describes how to run
   ▼
Container in ECS
```

---

# ⚙️ ECS Service

After creating the task definition, an ECS service can keep the application running.

For example:

```text
Desired Tasks = 1
Running Tasks = 1
```

means ECS should maintain one running copy of the application.

If a task fails, the service can work toward restoring the desired number of tasks.

During deployment, I successfully reached:

```text
1 Desired
1 Running
0 Pending
```

This was an important milestone because it confirmed that the application container was running successfully in ECS.

---

# 🌐 Application Load Balancer

Running a container is not enough.

Users also need a way to reach the application.

An **Application Load Balancer (ALB)** was connected with the ECS service.

The request flow becomes:

```text
Internet User
      │
      ▼
Application Load Balancer
      │
      ▼
Target Group
      │
      ▼
ECS Container
      │
      ▼
Node.js Application
```

The load balancer provides an entry point for incoming HTTP traffic.

---

# 🎯 Target Groups

The load balancer forwards requests to a target group.

The target group keeps track of the application targets receiving traffic.

Conceptually:

```text
Load Balancer
      │
      ▼
 Target Group
      │
      ▼
ECS Application
```

This helped me understand how AWS networking components connect a public request to a container running inside ECS.

---

# ❤️ Health Checks

The target group needs to determine whether the application is healthy.

Health checks periodically send requests to the application.

Conceptually:

```text
Load Balancer
     │
     │ Health Check
     ▼
Application
     │
     ├── Healthy   → Receive traffic
     │
     └── Unhealthy → Don't route normally
```

During the deployment, the target became healthy and the ECS service successfully ran the application.

---

# 📊 Monitoring

AWS provides monitoring information for running services.

While testing the deployment, I observed metrics such as:

- CPU utilization
- Memory utilization
- Running task count
- Deployment status
- Target health

This helped me understand that deployment is not only about starting an application.

After deployment, we also need to observe whether the application is healthy.

---
<img width="1708" height="920" alt="blured section to upload on github repo" src="https://github.com/user-attachments/assets/3769f6fd-98f7-4ee5-8dd5-cd4c99f8411f" />


# 🚀 Complete Deployment Flow

The complete process I practiced can be summarized as:

```text
1. Develop Application
          │
          ▼
2. Build Frontend
          │
          ▼
3. Serve Production Files
          │
          ▼
4. Create Dockerfile
          │
          ▼
5. Build Docker Image
          │
          ▼
6. Test Container
          │
          ▼
7. Configure AWS IAM
          │
          ▼
8. Create Amazon ECR Repository
          │
          ▼
9. Push Docker Image to ECR
          │
          ▼
10. Create ECS Cluster
          │
          ▼
11. Create Task Definition
          │
          ▼
12. Create ECS Service
          │
          ▼
13. Configure Load Balancer
          │
          ▼
14. Configure Target Group
          │
          ▼
15. Verify Health Checks
          │
          ▼
16. ECS Task Running
          │
          ▼
17. Application Accessible
```

---

# 🧠 Challenges and Learning

This project wasn't only about following deployment commands.

Several concepts became clearer while actually implementing the deployment.

## Understanding Docker Images

Initially, Docker images and containers seemed similar.

After practicing, the difference became clearer:

```text
Image = Package / Blueprint
Container = Running instance of image
```

---

## Understanding ECR vs ECS

Another important distinction was understanding the responsibilities of ECR and ECS.

```text
ECR
│
└── Stores Docker Images

ECS
│
└── Runs Containers
```

They work together but solve different problems.

---

## Understanding ECS Components

ECS contains several concepts:

```text
Cluster
Task Definition
Task
Service
```

Deploying the application helped me understand how these components connect rather than learning them only theoretically.

---

## Understanding Load Balancing

Another major learning area was understanding why the application needs a load balancer and target group.

Instead of thinking:

```text
User → Container
```

the deployment taught me to think:

```text
User
  ↓
Load Balancer
  ↓
Target Group
  ↓
Healthy ECS Task
  ↓
Application
```

---

# 💰 AWS Cost Management

Cloud resources can continue generating costs when they remain active.

After completing the deployment practice, I removed the resources that were no longer required.

This included checking resources associated with the deployment instead of simply leaving everything running.

The project therefore also taught me an important cloud principle:

> Resource cleanup is part of cloud engineering.

For learning projects, resources should be deleted when they are no longer required.

---

# 🔒 Security Considerations

While working with AWS, I also learned that credentials and account information should never be committed to public repositories.

Sensitive information should not be stored directly in GitHub.

Examples include:

```text
AWS Secret Access Keys
Access credentials
Passwords
Private keys
Environment secrets
API tokens
.env files
```

Files containing secrets should be excluded using `.gitignore`.

For public documentation screenshots, sensitive identifiers should also be removed or redacted where appropriate.

---

# 📁 Repository Structure

A simplified representation of the repository is:

```text
docker-aws/
│
├── Backend/
│
├── Frontend/
│
├── .dockerignore
├── .gitignore
├── Dockerfile
├── README.md
│
└── Documentation/
```

The exact internal frontend and backend structure may change as the application continues to evolve.

---

# 📝 `.dockerignore`

The `.dockerignore` file helps prevent unnecessary files from being copied into the Docker build context.

This can reduce image-building overhead and prevent unnecessary local files from becoming part of the image.

---

# 📝 `.gitignore`

The `.gitignore` file prevents unnecessary or sensitive files from being committed to Git.

Typical examples include:

```text
node_modules/
.env
*.pem
*.key
```

This is especially important when working with cloud credentials.

---

# 🔁 Local Development vs Cloud Deployment

One major learning outcome was understanding the difference between local and cloud execution.

### Local

```text
My Laptop
   │
   ├── Node.js
   ├── npm
   ├── Source Code
   └── Local Server
```

### Docker

```text
My Laptop
   │
   ▼
Docker Image
   │
   ▼
Container
```

### AWS

```text
My Laptop
   │
   │ Push Image
   ▼
Amazon ECR
   │
   ▼
Amazon ECS
   │
   ▼
AWS Infrastructure
```

The same application can therefore move from local development toward a reproducible cloud environment.

---

# 📚 What I Learned

This project gave me hands-on exposure to both application development and cloud deployment.

### Real-Time Development

I practiced:

- Real-time communication
- Socket.IO
- Client/server event communication
- Collaborative application concepts
- Conflict-resolution concepts
- CRDT fundamentals
- Replica synchronization concepts

### Docker

I learned:

- Why containers are useful
- Difference between image and container
- Dockerfile structure
- Docker build process
- Docker run process
- Container ports
- Production packaging
- `.dockerignore`

### AWS

I practiced:

- AWS IAM
- IAM permissions
- Amazon ECR
- Amazon ECS
- ECS clusters
- Task definitions
- ECS services
- Running tasks
- Application Load Balancer
- Target groups
- Health checks
- Basic CloudWatch monitoring
- AWS resource cleanup

### Git & GitHub

I also practiced:

- Git repositories
- Commits
- Remote repositories
- GitHub pushes
- `.gitignore`
- Project documentation
- Maintaining deployment code in version control

---

# 🔮 Future Improvements

This repository represents my current learning stage and can be extended further.

Some areas I want to explore include:

### CI/CD

Automating:

```text
Code Push
   ↓
Build
   ↓
Docker Image
   ↓
ECR
   ↓
ECS Deployment
```

This could be implemented using tools such as Jenkins or GitHub Actions.

### Infrastructure as Code

Instead of manually creating every AWS resource, infrastructure could eventually be defined using tools such as:

- Terraform
- AWS CloudFormation

### HTTPS

The application could be configured with:

- Custom domain
- SSL/TLS certificate
- HTTPS listener

### Better Monitoring

Monitoring could be improved with:

- CloudWatch logs
- CloudWatch alarms
- Application metrics
- Error monitoring

### Container Optimization

The Docker image could also be improved through:

- Smaller base images
- Better layer caching
- Multi-stage Docker builds
- Reduced dependencies

These are areas I plan to explore as I continue learning Docker, AWS and DevOps.

---

# 📌 Project Status

The application was successfully containerized and deployed on AWS ECS during the learning process.

The deployment was tested with:

```text
ECS Service: Active
Desired Tasks: 1
Running Tasks: 1
Deployment: Successful
Target Health: Healthy
```

The cloud resources were later removed when they were no longer required to avoid unnecessary AWS usage and charges.

Therefore, a permanent live URL may not currently be available.

---

# 🎓 Purpose of This Repository

This repository is not only the source code for a collaborative editor.

It also documents my practical learning journey through:

```text
Web Development
      ↓
Real-Time Communication
      ↓
Distributed-System Concepts
      ↓
Docker
      ↓
Containerization
      ↓
AWS IAM
      ↓
Amazon ECR
      ↓
Amazon ECS
      ↓
Load Balancing
      ↓
Cloud Deployment
```

Instead of only studying Docker and AWS theoretically, I wanted to deploy an actual application and understand how the individual services connect.

---

# 🙌 Conclusion

This project helped connect several concepts that I had previously learned separately.

I started with a real-time web application and then learned how to prepare it for production, package it into a Docker image, store the image in Amazon ECR, run the container using Amazon ECS and expose the application through an AWS Application Load Balancer.

More importantly, the project helped me understand the responsibility of each component:

```text
React        → Frontend
Node.js      → Backend
Socket.IO    → Real-time communication
CRDT         → Conflict-resolution concepts
Docker       → Application containerization
ECR          → Container image storage
ECS          → Container orchestration
ALB          → Traffic distribution
IAM          → Access control
CloudWatch   → Monitoring
GitHub       → Source-code management
```

This repository will continue to be updated as I learn more about **AWS, Docker, CI/CD, system design and DevOps**.

---

## 👨‍💻 Author

**Ankit Singh**

Learning and building with:

`JavaScript` • `React` • `Node.js` • `Docker` • `AWS` • `System Design` • `DevOps`

---

⭐ If you find this learning project useful, feel free to explore the repository.
