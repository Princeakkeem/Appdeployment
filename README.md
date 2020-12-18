# Appdeployment
# This deployment consists of three files which are AWS cloudformation files. These files are written in yaml language and needs to be run via AWS cloudformation console directly.

Steps to setup the environment are in this order:-

1. Fork the sample-service github repo as we will be setting up env from our account.
2. In the sample-service github repo push buildspec.yml file. This file will provide steps to AWS codebuild service on
   how to build the repo and produce container image from it.
3. Deploy cicd_build.yml file via AWS cloudformation console. This will create ECR repository in AWS which will be used 
   to store container images and Codebuild project which will be responsible for building the container images.
4. Once step 3 is done go to codebuild section in AWS console and manually run the build, this will create the first
   image and push the image to ECR repository.
5. Now go to EC2 section and make sure that keypair is created. Download the private key if new keypair is being created.
6. Now deploy ecs_cluster.yml file via cloudformation console. This will need certain parameters which are:-
   ContainerName:- Any name for container 
   ContainerPort:- Port on which application will run. For sample-service it is 5000
   VPCId:- ID of the vpc in which deployment will happen.
   Subnet1:- First of the 2 subnets that will be created.
   Subnet2:- Second of the 2 subnets that will be created.
   KeyName:- Name of key created in step 5.
   ImageId:- AMI id that will be used to deploy instance. It will change depending on which region is being used for deployment.
   InstanceType:- Type of instance.
   ECSRepoName:- ECR repo name created in step3.
   ImageTag:- Tag of the image that needs to be deployed.
7. Once step 6 is done successfully it will create a ECS cluster which is running application. Go to EC2 section and click on the
   load balancer created in step 6. Copy load balancer DNS name and open in browser it should load the app.
8. Now go to S3 section in AWS and create a S3 bucket which will be used as artifact store.
9. Go to codebuild section in AWS console, go to setting and create new connection. This will be used in step 10 to connect to github.
10. Now deploy cicd_pipeline.yml via cloudformation. It will take following parameters:-
   ArtifactStore :- Name of bucket created in step 8
   CodeBuildProject :- Name of codebuild project created in step 3
   ConnectionArn :- Connection Arn created in step 9.
   RepositoryId :- Repository Id is repo name from github in <username>/<reponame> format.
   RepositoryBranch :- branch of github repository that should be deployed.
