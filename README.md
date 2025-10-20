# Day 1: CI/CD Pipeline for a Node.js Application

This repository contains a simple Node.js "Hello World" application. The primary goal of this project was to set up a complete CI/CD pipeline using GitHub Actions to automatically test, build, and deploy the application as a Docker container to Docker Hub.

This project was built from scratch

## CI/CD Pipeline Explained

The pipeline is defined in the `.github/workflows/main.yml` file and performs the following steps on every push to the `main` branch:

1.  **Checkout Code**: The GitHub Actions runner checks out the latest version of the code.
2.  **Setup Node.js**: It installs Node.js version 18 to run tests.
3.  **Install Dependencies**: Runs `npm install` to get the required packages.
4.  **Run Tests**: Executes `npm test` to ensure code quality (CI step).
5.  **Login to Docker Hub**: Securely logs into Docker Hub using secrets stored in GitHub.
6.  **Build and Push Image**: Builds a Docker image based on the `Dockerfile` and pushes it to Docker Hub with the tag `latest`. This is the Continuous Deployment (CD) step.

### Workflow Code

```yaml
# A descriptive name for your workflow
name: Build and Push Docker Image

# 1. Trigger: This section defines when the workflow will run
on:
  push:
    branches: [ "main" ] # Runs only on a push to the main branch

# 2. Jobs: A workflow is made up of one or more jobs
jobs:
  # This job is named 'build-and-push'
  build-and-push:
    # 3. Runner: The type of virtual machine the job will run on
    runs-on: ubuntu-latest

    # 4. Steps: A sequence of tasks to be executed
    steps:
      # Step 1: Check out the code from your repository
      - name: Checkout repository
        uses: actions/checkout@v4

      # Step 2: Set up Node.js for the test step
      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'

      # Step 3: Install dependencies
      - name: Install dependencies
        run: npm install

      # Step 4: Run tests (CI part)
      - name: Run tests
        run: npm test

      # Step 5: Log in to Docker Hub
      - name: Login to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}

      # Step 6: Build the Docker image and push it to Docker Hub (CD part)
      - name: Build and push Docker image
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: ${{ secrets.DOCKERHUB_USERNAME }}/nodejs-demo-app:latest
```

## How to Run the Application

#### 1. You can run the deployed application using Docker:

```bash
# This command pulls the latest image and starts the container in the background
docker run --rm -d -p 8080:8080 --name nodejs-app adityajareda/nodejs-demo-app:latest
```
The `--rm` flag in the docker run command automatically removes the container when it is stopped.

### 2. To verify the application is running, you can use curl:

```bash
curl http://localhost:8080
# You should see the output: Hello World!
```


#### 3. To stop the application:

```bash
docker stop nodejs-app
```
