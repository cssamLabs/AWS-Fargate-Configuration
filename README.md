# AWS-Fargate-Configuration

Configuration of ECS Fargate starts from defining a Task. To define a Task with docker we need to dockerize the code base and need to upload to ECR.

Make Docker image

```docker build -t <name> .
```

Tag the image
```
docker tag aws-rds-node-api:latest 9999999999999.dkr.ecr.us-east-1.amazonaws.com/aws-rds-node-api:latest
```

Retrive login passward for he repository
```
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 999999999999.dkr.ecr.us-east-1.amazonaws.com
```

Push the image to ECR
```
docker push 9999999999.dkr.ecr.us-east-1.amazonaws.com/aws-rds-node-api:latest
```
![Docker-Image-Push-ECR](https://user-images.githubusercontent.com/6191308/162857287-0b19e838-3fc8-4e21-b0c9-06f9a7085625.png)

Now the image is ready in the ECR for deployment
![AWS-ECR-Image](https://user-images.githubusercontent.com/6191308/162857398-4b4c9c99-42ca-4d44-88c9-504c1157e325.png)


Fargate Task Configuration
There are two things need to do before start the Task Configuration. There must be Task Role. Other one is define production environment variables.

Create production.env 
I created S3 bucket to store production.env under the folder cssamlabs/lab-node-atlas-api/.
![Env-S3](https://user-images.githubusercontent.com/6191308/162863264-f1e67791-dc5a-431a-a7a6-2dc493b60905.png)

Then create a Task Execution Role. I added RDSFullAccess since application needs to access RDS. 
![ECS-Task-Execution-Role](https://user-images.githubusercontent.com/6191308/162876291-6beaf03c-aacd-4e25-b685-e362c9650d21.png)

There must be a policy to access S3 to read productio.env file.
![Access-S3-Env-Role](https://user-images.githubusercontent.com/6191308/162876383-9c6a29ba-ca8e-4b9b-980e-e075272de387.png)

Now start configuring Task Definition
![Task-Definition-Configuration-1](https://user-images.githubusercontent.com/6191308/162876470-b83dbdb0-a25e-4b34-986b-774ceb10ac7e.png)

![Task-Definition-Configuration-2](https://user-images.githubusercontent.com/6191308/162876510-6be6b40e-0ae4-4095-b140-e30af022fb88.png)

Need to add a container
![Fargate-Container-add](https://user-images.githubusercontent.com/6191308/162876549-a7073b21-06ea-4fd8-94e3-138fcba9f41b.png)

Copy image name from ECR
Port is map to 3040 where NodeJS application configured in Dockerfile.

Configure Production env in Environment Files section by giving the file's ARN
![Fargate-Env-S3-Add](https://user-images.githubusercontent.com/6191308/162876918-a16be2f2-c232-43cb-a6ab-438953e272a3.png)

Once create Task Definition is like
![Task Definition](https://user-images.githubusercontent.com/6191308/162560660-7b1329ec-8d25-4011-a1d5-7a8d758126e2.png)


Next step is to create Fargate Cluster
Configured Cluster is
![Lab-Cluster](https://user-images.githubusercontent.com/6191308/162560684-398d8777-7206-46e4-9c04-72720204a4d3.png)


Create a Container Service
A Service Name needed. Select the Cluster created above. 
![Fargate-Service-Create](https://user-images.githubusercontent.com/6191308/162879405-71d41f2a-08c2-4223-95e5-aaa92259e044.png)

Select the VPC and Subnects. Usually it creates new Security Group.
![Fargate-Service-Configuration](https://user-images.githubusercontent.com/6191308/162879720-3e90973c-83f3-4b0c-88d6-153b7eca9398.png)

Load Balancing
![Fargate-service-ALB](https://user-images.githubusercontent.com/6191308/162880081-43aadd39-f488-452d-83f3-0cb5cd076dc8.png)

Port Configuration
![Fargate-Service-ALB-Ports](https://user-images.githubusercontent.com/6191308/162880171-2224e16b-0c5d-4698-a5e6-15704f526009.png)

Fargate Service Configured
![Labs - Fargate Service](https://user-images.githubusercontent.com/6191308/162560694-24d52861-d9ab-4856-b58f-5b495ada076e.png)


Now Creating a Task from Task Definition could carryout.
Task running in Fargate Service
![Fargate-Service-Running](https://user-images.githubusercontent.com/6191308/162880275-95703242-3aa3-415e-9d9b-dd6008e08b67.png)

![Task](https://user-images.githubusercontent.com/6191308/162560642-200dbbd0-d490-4553-b2cc-6677268490a0.png)


