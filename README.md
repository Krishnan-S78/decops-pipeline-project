Project Description

This project demonstrates the implementation of a complete Continuous Integration and Continuous Deployment (CI/CD) pipeline using modern DevOps tools and practices. It showcases how to automate the entire software delivery lifecycle—from code commit to production-ready containerized deployment—using GitHub Actions as the CI/CD orchestrator and Docker for containerization.

The project features a simple Node.js Express web application that serves as a practical example for understanding CI/CD workflows. Every code push to the main branch triggers an automated pipeline that runs tests, builds a Docker image, and deploys it to DockerHub. 






Prerequisites

Ensure the following are installed

 Node.js
 Docker
 Git
 Github Account
 DockerHub Account

and verify it's installations ( If the version conflicts make it to the version as mentioned below )

node --version    # Should output: v18.x.x or higher
npm --version     # Should output: 9.x.x or higher
docker --version  # Should output: Docker version 20.x.x or higher
git --version     # Should output: git version 2.x.x or higher




Project Scope
What's Included:

1.Node.js Express application
2.Docker containerization
3.Github Actions CI/CD workflow
4.Automated testing integration
5.DockerHub image registry integration
6.Secrets management implementation





Step-by-Step Implementation

1.  Local Environment Setup

- Install Node.js and npm  
- Install Docker and add current user to Docker group  
- Install Git and configure username and email  
- Create project directory and logout/login for Docker permission changes  



2 Create the Application

2.1
    Create a directory for the project
    CD into the directory

2.2 Initialize Node.js Project

    npm init -y
    npm install express


2.3 Create server.js with the following content:

```javascript
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send('Hello from the Automated Pipeline!');
});

app.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

**Purpose**: This creates a simple Express web server that responds with a greeting message.

  
2.4 Update the package.json scripts

Edit `package.json` and add the following to the `scripts` section:
```json
{
  "name": "decops-pipeline-project",
  "version": "1.0.0",
  "scripts": {
    "test": "echo 'Test Passed!' && exit 0",
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^4.18.2"
  }
}
```

2.5 Create Dockerfile -including

    FROM node:18-alpine
    WORKDIR /usr/src/app
    COPY package*.json ./
    RUN npm install
    COPY . .
    EXPOSE 3000
    CMD [ "node", "server.js" ]

**Dockerfile Explanation:**
- `FROM node:18-alpine`: Lightweight Node.js 18 base image
- `WORKDIR /usr/src/app`: Sets working directory
- `COPY package*.json ./`: Copies dependency files (leverages Docker cache)
- `RUN npm install`: Installs dependencies
- `COPY . .`: Copies application code
- `EXPOSE 3000`: Documents the port
- `CMD`: Command to run when container starts



2.6 Create a .gitignore file       ##Prevents committing `node_modules` (will be installed by Docker) and sensitive environment variables.

    cat > .gitignore << 'EOF'
node_modules/
.env
.DS_Store
*.log
EOF



3. DockerHub Setup

   Create an access token from New Access Token from the Security section in Account Settings



4. GitHub repository setup

4.1 Create a new repository ( Public )

   #Initialize git

    git init
    git add .
    git commit -m " Initial app and Docker files "


   #Add remote

    git remote add origin " url of your repository "


   #Push to GitHub

    git branch -M main
    git push -u origin main

    authenticate the github tokens when asks for passwords



5.  Configure GitHub secrets  

5.1 Add secrets to Github

    In your repository on github , Secrets and Variables in Settings, then actions -> New repository secret

    Add the DOCKER_USERNAME and DOCKER_PASSWORD as two seperate secrets
     (add token created in Dockerhub in Docker_Password Secret section )   



6.  Create CI/CD Pipeline 

6.1 create the workflows directory inside .github

6.2 create the main.yml pipeline file

Create `.github/workflows/main.yml` with the following content

mkdir -p .github/workflows
vi .github/workflows/main.yml

add this yml content to main.yml file

name: Automated Build and Deploy

# Trigger on push to the main branch
on:
  push:
    branches: [ "main" ]

jobs:
  build-and-push:
    runs-on: ubuntu-latest

    env:
      IMAGE_NAME: ${{ secrets.DOCKER_USERNAME }}/decops-pipeline-project

    steps:
      # Get the code
      - name: Checkout repository
        uses: actions/checkout@v4

      # Run the test step
      - name: Run Tests
        run: npm test

      # Login to DockerHub using secrets
      - name: Login to DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      # Build and Push the Docker image
      - name: Build and Push Docker Image
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: ${{ env.IMAGE_NAME }}:latest
```

**Workflow Breakdown:**
- **on.push**: Triggers when code is pushed to main branch
- **runs-on**: Uses Ubuntu environment for pipeline execution
- **env.IMAGE_NAME**: Sets Docker image name using secret username
- **actions/checkout@v4**: Downloads your repository code
- **npm test**: Runs test script from package.json
- **docker/login-action@v3**: Authenticates with DockerHub
- **docker/build-push-action@v5**: Builds and pushes Docker image

6.3 Commit and push to trigger the pipeline

    git add .
    git commit -m "feat: Add CI/CD pipeline"
    git push origin main

     Verify the pipline is running from the Actions tab in your GitHub repository.



outcomes ->

1.Design and implement a fully automated CI/CD pipeline using GitHub Actions
2.Containerize web applications using Docker and manage images in container registries
3.Configure secure workflows using GitHub Secrets for credentials management
4.Automate the complete software delivery lifecycle from development to deployment
5.Troubleshoot common CI/CD pipeline failures and containerization issues
6.Apply DevOps principles to real-world application deployment scenarios
7.Understand the relationship between source control,build automation, and deployment
8.Implement best practices for continuous integration and delivery


Project Architecture

Developer -> Git Push -> GitHub -> GitHub Actions -> Docker Build -> DockerHub -> Deployment


Pipeline Stages

1.Code Commit: Developer pushes code to GitHub repository
2.Trigger: GitHub Actions detects push to main branch
3.Checkout: Pipeline retrieves latest code
4.Test: Automated tests execute
5.Build: Docker image is built from Dockerfile
6.Push: Image is pushed to DockerHub registry
7.Deploy: Ready for deployment to any environment



Use Cases

1. Microservices Architecture: Automate deployment of individual services

2. Web Application Deployment: Continous delivery of web apps

3. API Development: Automated testing and deployment of REST APIs

3. Multi-Environment Deployments: Extend to dev, staging, and production

4. Team Collaboration: Enable multiple developers to deploy safely




Project is success when

. Every push to main branch triggers the pipeline automatically
. All pipeline stages complete without manual interventions
. Docker images are successfully built and pushed to DockerHub
. Application runs successfully from deployed container
. Pipeline completes in under 5 minutes
. Zero manual deployement steps required
