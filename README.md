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
 
 

Automate the via CI/CD using the Deploy.yml file in github workflows

![image](https://user-images.githubusercontent.com/106523671/173546305-f2e625b2-db7d-4484-8ef6-2fd6a1afc40d.png)
![image](https://user-images.githubusercontent.com/106523671/173546336-91022d92-8bc4-488f-91e9-7caa5eb7e6c7.png)
![image](https://user-images.githubusercontent.com/106523671/173546393-9037f8f9-7cfe-4991-bc22-6f815e26bb86.png)


 
 
 
