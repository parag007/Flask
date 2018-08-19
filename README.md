# Flask
Deploying a Containerized Flask Application with AWS ECS and Docker

Step 1 – Prerequisites
Let’s make sure we have everything we need to make this work:
1. An AWS Account. 
2. The AWS CLI Installed and Configured (with enough permissions to take all the actions we need to take). 
3. Docker (Docker commands may require sudo, or you can add your current user to the docker group). 
4. The code for this project. You can run git clone https://github.com/parag007/Flask.git && cd cda-2018-flask-app && git checkout ecs-master 
(Make sure you change to the other branch as this contains code specific to ECS) 

Step 2 – Make Our Container Image

Build the docker image locally with: sudo docker build -t flask-app .
Run this command to run your Docker image locally sudo docker run -p 9090:80 flask-app 
Open up a web browser and enter “localhost:9090”into your web browser’s URL bar you’ll see the site running locally in our new container image. 


Step 3 – Send our container up to AWS

Now we’re going to send our newly created and tested container up to AWS.
Start by creating a repository in the Elastic Container Registry:
aws ecr create-repository --repository-name flask-app007
Then run this to get a command you can use for logging in to the ECR repository you’ve just created:
aws ecr get-login --region us-east-1 --no-include-email
Then copy and paste the output of that command back into the terminal and run it. This will actually authenticate you with the Elastic Container Repository so you can push your Docker image into it. 
Now that we’re logged into ECR, we can put our image into ECR. Let’s do this by:
	
1. Making sure we have our image locally by running docker image ls 
2. Tagging the image by running docker tag flask-app:latest ACCT_ID.dkr.ecr.us-east-1.amazonaws.com/ (where ACCT_ID is your own AWS account ID) 
3. Pushing the image up to AWS ECR with docker push ACCT_ID.dkr.ecr.us-east-1.amazonaws.com/flasktest (again, using your own AWS account ID in place of ACCT_ID) 
Boom! You should have a container image up in AWS.


Step 4 – Deploy our web application using ECS

The container image that we built locally is now up in the AWS cloud, inside of ECR. Since we’ve done this, we can use that same container image to deploy our application using Amazon ECS. 
First, let’s make sure we can see the image we deployed inside of ECR. We do this by navigating to the ECR portion of the ECS console under Amazon ECR > Repositories. It should look something like this, though the repository name and URL for what you uploaded might be a little different.
This means that we have a container image for the application stored inside of AWS and we can now move on to ECS to get it running! Before you do though, let’s make sure to copy down the URI for the repository (we’ll need it shortly). In the AWS console, go to the ECS section and navigate to the Clusters tab. Then press the “Get Started” button.
In Container definition you’ll need to press “Configure” to set a custom container:
When configuring the container definition, we need to change the value in the box next to “Image*” to the URL of the image that we copied from the ECR page. This allows us to reference the ECR image in our container definition.
We’ll also need to set a memory limit on this page – just use a Hard Limit of 128. Also, make sure to add a TCP port mapping on port 80 as shown in the image before pressing the Update button.
After this update, we should be ready to move on to the next step. We don’t need to change anything regarding the task definition, so just press the “Next” button. 
Further down on that page there should be a blue button called Create:
Once we press it, we should see AWS start creating a bunch of resources for the new service. Let this finish up and then press the “View service” button.

Then from the tasks tab, we can click on the task that’s running for this new service. 
Then click on the ENI ID link next to ENI Id:

This will redirect us to the AWS EC2 console which should show an instance running. We can grab the IPV4 IP address of that instance and paste it into a web browser.


Congratulations! We’ve just deployed a containerized application using AWS, Docker, ECS, and Flask! 
