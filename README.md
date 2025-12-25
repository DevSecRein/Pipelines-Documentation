# Jenkins Pipeline Documentation
For Docker, log in or create an account, then install docker desktop if you don't have it. 

Open Docker Desktop. Then go to Images, search for "Jenkins" and pull "Jenkins/Jenkins"
![Screenshot1](./Screenshots/Screen%20Shot%202025-01-21%20at%2020.10.04.png)

For Jenkins, open the command line interface and and run this command
	docker run -p 8080:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts-jdk11
		The command line will spit out your Jenkins password (make sure to save it somewhere)
			![Screenshot2](./Screenshots/Screen%20Shot%202025-01-21%20at%2020.24.13.png)

Access Jenkins via the web browser
	[http://localhost:8080](http://localhost:8080/)
		To do this you must make sure that the container with the ports is running
		![Screenshot](./Screenshots/Screen%20Shot%202025-02-04%20at%2023.06.56.png)
		*Note: Don't delete the second Jenkins container*

Paste the password
	![Screenshot3](./Screenshots/unlock-jenkins.png)

Customize Jenkins and use suggested plugins
	![Screenshot4](./Screenshots/Screen%20Shot%202025-01-21%20at%2020.33.38.png)
	![Screenshot5](./Screenshots/Screen%20Shot%202025-01-24%20at%2020.58.23.png)

Once it is complete you should see the homescreen. From the homescreen go to Manage Jenkins > Plugins > Available Plugins

![Scrrenshot6](./Screenshots/Screen%20Shot%202025-01-25%20at%2009.21.11.png)

Search the following in Available Plugins(search with lower case letters):

	AWS
		-AWS Credentials
		-Pipeline: AWS Steps
		-Amazon EC2
		-Amazon Elastic Container Service (ECS) / Fargate 
		-Amazon S3 Bucket Credentials 
		-AWS CodeBuild
		-AWS CodeDeploy
		-AWS Lambda 
		-AWS CodePipeline 
		-AWS Secrets Manager SecretSource 
		-Configuration as Code AWS SSM secrets
		-CloudFormation 
		-AWS SAM
	Terraform
		-Terraform
	Google
		-Kubernetes
		-Google Cloud Storage
		-Google Kubernetes Engine
		-Google Cloud Platform SDK::Auth
		-Pipeline: GCP Steps
	DevSecOps
		-Snyk Securityﾠ
	Sonar
		-SonarQube Scannerﾠ
	Aqua
		-Aqua Security Scannerﾠ 
		-Aqua MicroScannerﾠ
		-Aqua Security Serverless Scannerﾠ
	GitHub
		-GitHub Integration
		-GitHub Authentication
		-Pipeline: GitHub
		-Pipeline GitHub Notify Step

Go to Jenkins > Manage Jenkins > Credentials and click global. Then add AWS credentials to Jenkins
![Screenshot7](./Screenshots/Screen%20Shot%202025-02-05%20at%2005.45.26%20(2).png)

Then click New Item, name the item, click Pipeline, and ok
![Screenshot8](./Screenshots/Screen%20Shot%202025-02-05%20at%2005.47.24%20(2).png)

Then change the pipeline configurations to look as such but use the repository url of the repo you want to use
![Screenshot9](./Screenshots/Screen%20Shot%202025-02-05%20at%2006.51.09%20(2).png)

# Test Pipeline
Fork the below repository
	https://github.com/derrickSh43/autoScale
		Git clone it to edit in Desktop

In the command line terminal type "docker ps" to retrieve info on running container. Then run "docker exec -it --user root \[container name] bash" (replacing "\[container name] with actual name )

Then run the command "apt update && apt install -y awscli" to install the CLI in your Jenkins container
![Screenshot10](./Screenshots/Screen%20Shot%202025-02-05%20at%2006.20.59.png)

Then exec the following CLI commands line by line
	- mkdir -p /home/jenkins/bin
	- curl -fsSL https://releases.hashicorp.com/terraform/1.5.7/terraform_1.5.7_linux_amd64.zip -o /home/jenkins/terraform.zip
	- unzip /home/jenkins/terraform.zip -d /home/jenkins/bin
	- rm /home/jenkins/terraform.zip
	- export PATH="home/jenkins/bin:$PATH"
		*Note if terraform doesn't end up working, execute "mv /home/jenkins/bin/terraform /usr/local/bin" command and repeat the 2nd through 5th CLI commands*

To confirm that it works run "terraform --version" to see if terraform is installed

In Jenkins hit build now to build the infrastructure
![Screenshot11](./Screenshots/Screen%20Shot%202025-02-05%20at%2008.31.55%20(2).png)

Check the console log to monitor changes and troubleshoot as needed
![Screenshot12](./Screenshots/Screen%20Shot%202025-02-05%20at%2008.35.19%20(2).png)
 ***NOTE: SCROLL DOWN THE CONSOLE LOG AND CLICK DEPLOY FOR TERRAFORM TO APPLY***

Once successfully deployed the pipeline should look like this:
![Screenshot13](./Screenshots/Screen%20Shot%202025-02-05%20at%2008.41.52%20(2).png)

To tear down execute
	cd var/jenkins_home/workspace/\[pipeline name]
Then
	export AWS_ACCESS_KEY_ID="\[AWS access key with CLI permissions]"
	export AWS_SECRET_ACCESS_KEY="\[Associated Secret Key to Above]"
	export AWS_REGION="\[region of choice]"
	export PATH=$PATH:/path/to/terraform

Then you can terraform destroy -auto-approve from the command line as usual!
	![Screenshot14](./Screenshots/Screen%20Shot%202025-02-05%20at%2018.28.36.png)

