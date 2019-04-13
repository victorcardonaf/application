
# Ansible and Jenkins Implementation

There are several processes that are required to deploy the app correctly. The automation tool used to develop this activity is ansible. We need an ansible controller to ensure that the playbook developed can be carried out. Please follow the next steps:

1. Run a docker container with the ansible controller
2. Deploy the playbook from the ansible controller
3. Connect to Jenkins Web Interface to create credentials and the pipeline job
4. Check the app running accessing from the web browser

Requirements:

Install Docker on the baseline OS (You can use a Windows Machine, MACOS or Linux OS. Please follow the instructions)
- https://docs.docker.com/install/linux/docker-ce/ubuntu/
- https://docs.docker.com/docker-for-mac/install/
- https://docs.docker.com/docker-for-windows/install/

**Run a docker container with the ansible controller**

Clone repo in the local machine.

Go to personal_project/ansible-base-build folder. Execute the next command:
```
docker build -t centos-ansible .
```
After the building process, run the container:
```
docker run  --privileged  -v /sys/fs/cgroup:/sys/fs/cgroup --cap-add=SYS_ADMIN --cap-add=NET_ADMIN --name=centos-ansible -v "folder-where-you-clone"/personal_project:/media -dti centos-ansible
```
Go inside from the container:
```
docker exec -ti docker-ansible /bin/bash
```

**Deploy the playbook from the ansible controller**

Inside from the container, the first thing that is required is to modify the export_aws_variables.sh file providing the access keys to deploy resources in AWS.
```
#!/bin/bash
export AWS_REGION=us-east-1
export EC2_ACCESS_KEY=xxxxxxx
export EC2_SECRET_KEY=xxxxxxxxxxxxxxxxxxxxxx
```

After modifying the file, execute it:
``` ./export_aws_variables.sh
```
Verify with the env command if the variables were configured correctly:
```
env
```
Now inside from the container, execute:
```
ansible-playbook -i inventory/hosts provision_aws.yml
```

This playbook will deploy resources in AWS. It will install a VPC inside from Virginia region and an EC2 t2.medium instance. In the instance will be installed Java, Docker and Jenkins. 


**Connect to Jenkins Web Interface to create credentials and the pipeline job**

The Jenkins public ip can be taken from the inventory/hosts file. You can see the existing value instead of the "jenkins-ip" value. This value is replaced when jenkins is deployed. 
```
[local]
localhost ansible_connection=local

[jenkins]
jenkins-ip ansible_user=ec2-user become=true ansible_ssh_private_key_file=/media/keys/keypair.pem
```
To connect to the Jenkins Server, open a web browser using 8080 port and http protocol:
```
http://jenkins-ip:8080
username: admin
password: admin
```

**Configure dockerhub Credentials**

Go to Credentials -> Add Credentials -> Configure username, password, ID and Description
```
username: "username used in dockerhub"
password: "password used in dockerhub"
ID: dockerhub -> Variable used in Jenkinsfile
```
**NOTE: Please use the same value to ID. This value is used in the Jenkinsfile**


**Create the pipeline job**

New Item -> Choose pipeline project and define a job name. Define the next parameters:

```Build Triggers Section
Check Poll SCM and put in the schedule * * * * * to check the repo each minute.
```

```Pipeline Section
In definition choose Pipeline script from SCM
In SCM choose Git
```

```Repositories Section
Repository URL: https://github.com/victorcardonaf/application.git
```

```Script Path Section
Jenkinsfile
```

Save Changes.The pipeline job is ready.

**Check the app running accessing from the web browser**

Open http://jenkins-ip:3000/ in your browser after the pipeline 


