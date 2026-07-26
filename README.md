# Jenkins CI/CD Pipeline using Docker

## Project Overview

This project demonstrates a simple Continuous Integration and Continuous Deployment (CI/CD) pipeline using Jenkins and Docker.

## Technologies Used

- Python
- Flask
- Jenkins
- Docker
- Git
- GitHub

## Pipeline Stages

- Checkout Source Code
- Build Docker Image
- Run Tests
- Login to Docker Hub
- Push Docker Image
- Deploy Docker Container

## Run Locally

Install dependencies

```bash
pip install -r requirements.txt
```

Run the application

```bash
python app.py
```

Open

```
http://localhost:5000
```

## Build Docker Image

```bash
docker build -t sample-app .
```

Run Container

```bash
docker run -d -p 5000:5000 sample-app
```

## Jenkins

Create a Pipeline Job.

Configure SCM.

Point to GitHub Repository.

Use Jenkinsfile.

Run Build.
