Project - Using Github Actions Deploy Web App on AWS ECS Cluster
Create amazon linux ec2 instance 
1)	Install Git 
          sudo yum update -y
          sudo yum install git -y
          git version
          git clone https://github.com/Ritesh-Goyal/restro-application.git
          ll
          cd history
                 history
2)	Install Docker
    docker info
    sudo systemctl start docker
    sudo systemctl enable docker
    sudo usermod ec2-user -aG docker
                  docker info
3)	Install maven
           sudo wget https://repos.fedorapeople.org/repos/dchen/apache-maven/epel-apache-maven.repo -O /etc/yum.repos.d/epel-apache-maven.repo  
           sudo sed -i s/\$releasever/6/g /etc/yum.repos.d/epel-apache-maven.repo
           sudo yum install -y apache-maven
           mvn -version
Using Maven Build the war file

In EC2 Instance 
                 cd restro-application/
                 mvn validate compile packge
                 mvn validate compile package
                 ll
                 ll target/
![image](https://user-images.githubusercontent.com/106523671/173545723-768da363-253e-411d-bc96-137e8082676e.png)
 
Using Dockerfile build Docker Image
![image](https://user-images.githubusercontent.com/106523671/173545938-81731bcb-b3c2-460e-ac8a-06e22a72cb90.png)

 
   cd restro-application/
   docker build -t anantharaju/restro-application:latest .
Push Docker Image on Docker Hub
   docker login
   docker push anantharaju/restro-application:latest
![image](https://user-images.githubusercontent.com/106523671/173546075-047f4e50-a304-49a0-b325-6ee29ab0cba9.png)

 
Create Task Definition on AWS ECS
 ![image](https://user-images.githubusercontent.com/106523671/173546133-5e85869c-e260-4c93-a66a-c2040f266923.png)
![image](https://user-images.githubusercontent.com/106523671/173546164-c3bee62a-b80b-459f-9d88-492b9702bf90.png)
![image](https://user-images.githubusercontent.com/106523671/173546202-8c58879f-046c-498a-941b-46d939568e33.png)
![image](https://user-images.githubusercontent.com/106523671/173546239-c17ec69f-81fe-4eff-86f5-6e1f0b89847c.png)
 
 

Automate the via CI/CD
Deploy.yml
name: Deploy to AWS ECS cluster

on:
 push:
   branches:
     - "main"

env:
 AWS_REGION: ap-south-1
 ECS_SERVICE: app-service
 ECS_CLUSTER: restro
 CONTAINER_NAME: restro-app

jobs:
  app_pipeline:
    name: "Restro-Deploy"
    runs-on: "ubuntu-latest"
    steps:
      - name: "App Code Checkout"
        uses: actions/checkout@v2
  
  - name: Setup of JDK 11
    uses: actions/setup-java@v3
    with:
      distribution: 'adopt'
      java-version: '11'

  - name: Build Application using Maven
    run: mvn clean compile package

  - name: Set up Docker Buildx
    uses: docker/setup-buildx-action@v2

  - name: Docker login
    uses: docker/login-action@v2
    with:
      username: ${{ secrets.DOCKERHUB_USERNAME }}
      password: ${{ secrets.DOCKERHUB_PASSWORD }}

  - name: Docker Build and push to DockerHub
    uses: docker/build-push-action@v3
    with:
      context: ./
      file: ./dockerfile
      push: true
      tags: |
        ${{ secrets.DOCKERHUB_USERNAME }}/restro-application:latest
        ${{ secrets.DOCKERHUB_USERNAME }}/restro-application:${{ github.sha }}
  
  - name: Configure AWS Credentials
    uses: aws-actions/configure-aws-credentials@v1
    with:
      aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
      aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
      aws-region: ${{ env.AWS_REGION }}

  - name: Download Amazon ECS task definition
    run: | 
     aws ecs describe-task-definition --task-definition restro-task --query taskDefinition > task-definition.json
     cat task-definition.json

  - name: Replace Docker image ID and create new Task definition
    id: task-def
    uses: aws-actions/amazon-ecs-render-task-definition@v1
    with:
      task-definition: task-definition.json
      container-name: ${{ env.CONTAINER_NAME }}
      image: ${{ secrets.DOCKERHUB_USERNAME }}/restro-application:latest

  - name: Deploy Amazon ECS task definition
    uses: aws-actions/amazon-ecs-deploy-task-definition@v1
    with:
      task-definition: ${{ steps.task-def.outputs.task-definition }}
      service: ${{ env.ECS_SERVICE }}
      cluster: ${{ env.ECS_CLUSTER }}
      wait-for-service-stability: true

  - name: Slack Notification
    uses: 8398a7/action-slack@v3
    with:
      status: ${{ job.status }}
      text: "The CI/CD Pipeline for Restro project executed successfully"
      fields: repo,message,commit,author,action,eventName,ref,workflow,job,took,pullRequest # selectable (default: repo,message)
      channel: "ci-cd_pipeline"
    env:
      SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }} # required
    if: always() # Pick up events even if the job fails or is canceled.
![image](https://user-images.githubusercontent.com/106523671/173546305-f2e625b2-db7d-4484-8ef6-2fd6a1afc40d.png)
![image](https://user-images.githubusercontent.com/106523671/173546336-91022d92-8bc4-488f-91e9-7caa5eb7e6c7.png)
![image](https://user-images.githubusercontent.com/106523671/173546393-9037f8f9-7cfe-4991-bc22-6f815e26bb86.png)


 
 
 
