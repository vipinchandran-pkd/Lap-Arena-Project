# 🚀 **DevOps Project: Lap Arena Application Deployment**

 **This is my own Devops Project. Developer only push the project code to github and all other files like dockerfile ,jenkinsfile and requirements.txt are created by me.**

  If you want to work on this project on you own dockerfile,jenkinsfile,requirements.txt.
Go through the link [Lapzone](https://github.com/didin2003/lapzone.git)

## **Phase 1: Initial setup and Deployment**

**Step 1: clone the repository**

```bash
git clone 
```

**Step 2:Test it locally**

```bash
sudo apt update
sudo apt install python3 python3-pip python3-venv -y
```
```bash
python manage.py migrate
python manage.py runserver 0.0.0.0:8000
```

It will generate an ip address copy ip and paste it on the browser.


Lapzone Project by Didin `<127.0.0.1:8000>`

<div align="center">
<img src="img/tested-locally.png" alt="Logo" height="500%" width="100%">
<p align="center"> Tested locally output </p>
</div>


## Deploy Application on Docker

**Step 1: Launch EC2 (ubuntu 22.04)**

- Provision an EC2 instance on AWS with Ubuntu 22.04,volume 29gb,t3.large. 
- Connect to the instance using SSH.
- Allow ports

  - SMTP 25
  - CUSTOM TCP 3000 - 10000
  - HTTP 80
  - HTTPS 443
  - SSH 22
  - CUSTOM TCP 6443
  - SMTPS 465
  - CUSTOM TCP 30000-32676
  - CUSTOM TCP 8000


**Step 2: update the Package**


```bash
sudo apt-get update
```


**Step 3: clone the repository**

```bash
git clone https://github.com/didin8080/Lap-Arena.git
```

**Step 4: Install python**

```bash
sudo apt update
sudo apt install python3 python3-pip python3-venv -y
```

**Step 5: create virtual environment**

```bash
python3 -m venv venv
source venv/bin/activate
```

**Step 6: Install Dependencies**

```bash
pip install django
pip install pillow
```

**Step 7: create and install requirements.txt**

```bash
pip freeze > requirements.txt
pip install -r requirements.txt
```


**Step 8: Install Docker on ubuntu**

```bash
sudo apt-get update

sudo apt-get install ca-certificates curl

sudo install -m 0755 -d /etc/apt/keyrings

sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc

sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

sudo usermod -aG docker ubuntu

sudo chmod 777 /var/run/docker.sock

newgrp docker

sudo systemctl status docker
```


**Step 9:Install Jenkins on ubuntu**


```bash
sudo apt update -y

wget -O - https://packages.adoptium.net/artifactory/api/gpg/key/public | sudo tee /etc/apt/keyrings/adoptium.asc

echo "deb [signed-by=/etc/apt/keyrings/adoptium.asc] https://packages.adoptium.net/artifactory/deb $(awk -F= '/^VERSION_CODENAME/{print$2}' /etc/os-release) main" | sudo tee /etc/apt/sources.list.d/adoptium.list

sudo apt update -y

sudo apt install temurin-17-jdk -y

/usr/bin/java --version

curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt-get update -y

sudo apt-get install jenkins -y

sudo systemctl start jenkins

sudo systemctl status jenkins
```

`publicIp:8080`


**Step 10: Install Docker Scout**


Make sure to Login to DockerHub account in browser

Login on terminal

```bash
docker login -u docker-username
```

Its asks for a password provide dockerhub password.

**Step 11: Install Trivy on ubuntu**


```bash
sudo apt-get install wget apt-transport-https gnupg

wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null

echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb generic main" | sudo tee -a /etc/apt/sources.list.d/trivy.list

sudo apt-get update

sudo apt-get install trivy
```


**Step 12: Create Dockerfile**

```bash
nano dockerfile
```

Paste it below

```bash
FROM python:3.11-slim

ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1

WORKDIR /app

COPY requirements.txt /app/

RUN pip install --upgrade pip && \
    pip install -r requirements.txt

COPY . /app/

EXPOSE 8000

CMD ["/bin/sh", "-c", "python manage.py migrate && python manage.py runserver 0.0.0.0:8000"]
```

### Install sonarqube 

**Step 1: Launch EC2 (ubuntu 22.04)**

- Provision an EC2 instance on AWS with Ubuntu 22.04,volume 29gb,t3.large. 
- Connect to the instance using SSH.
- Allow ports

  - HTTP 80
  - HTTPS 443
  - SSH 22
  - CUSTOM TCP 9000


**Step 2: Tnstall docker**

```bash
sudo apt-get update

sudo apt-get install ca-certificates curl

sudo install -m 0755 -d /etc/apt/keyrings

sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc

sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

sudo usermod -aG docker ubuntu

sudo chmod 777 /var/run/docker.sock

newgrp docker

sudo systemctl status docker
```


**Step 3: Install SonarQube using Docker**

```bash
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
```

`publicIp:9000`


**step 4: Install necessary plugins on Jenkins**

Goto Jenkins dashboard--> Manage Jenkins --> Plugins

- eclipse temurin installer
- sonarqube scanner 
- prometheus metrics 
- pipeline:stage view
- Docker 
- docker api
- docker commons
- docker build step
- docker plugin
- Kubernetes
- Kubernetes client api
- Kubernetes credentials
- Kubernetes credentials provider
- Kubernetes cli 


**step 5: Tools configuration in jenkins**

Goto Jenkins dashboard--> Manage Jenkins --> tools 
- add jdk,give name as jdk17 ,install automatically ,add installer, install from adoptium.net, select jdk-17.0.8.1+1 
- add sonarqube scanner--> name= sonar-scaner,insall automatically,
- add dependency check --> name= DP-Check ,install automatically ,add installer, install from github.com
- add docker --> name=docker,install automatically,add installer ,download from docker.com
- apply and save

**Step 6: ADD Credentials on Jenkins**

- On sonarqube -> admininstration-->security--> user--> rightside 3dots--> give any name --> generate token
- On jenkins --> manage jenkins-->credentials-->global--> add credentials--> kind=secret text --> on secret paste the token and give any name and description-->give any id and description-->create
- On jenkins --> manage jenkins-->credentials-->global--> add credentials-->kind=username with password--> username=give the username of dockerhub--> password=password of dockerhub-->create
- On jenkins --> manage jenkins-->credentials-->global--> add credentials--> kind=username with password--> username= provide your mail id -->password= the APP PASSWORD that you created --> id smtp.

**Step 7: System Configuration in Jenkins**

On jenkins--> manage jenkins--> system

- add sonarqube server -->give name as sonar-server--> on server url= paste the url of sonarqube -->select the authentication --> apply and save .
- Extended email notification --> On SMTP server= smtp.gmail.com --> SMTP port = 465 -->advanced --> enale use SSL --> on credentails = add credentials that we added for gmail
- E-mail Notification --> On SMTP server = smtp.gmail.com --> advanced --> enable use ssl --> smtp port 465--> enable use SMTP authentication --> username= your-email-id --> on password = app password `eg:(drsn anne afrd pcfx)`--> enable test configuration by sending email --> enter our email--> test configuration --> Goto your email you will get an test email from jenkins --> apply and save

**step 8: Create webhook in sonarqube**

- on jenkins--> manage jenkins--> system--> add sonarqube server -->give name as sonar-server--> on server url= paste the url of sonarqube with sonarqube-webhook eg:**(192.168.20.173:8080/sonarqube-webhook)** -->select the authentication --> apply and save .

**Step 9: Configure CI/CD pipeline in jenkins**

Before pasting the pipeline script, do the following changes in the script
1. In the stage 'Tag and Push to DockerHub', give your docker-hub username. Similar thing you should do in 'Deploy to kubernetes' stages
2. In post actions stage in pipeline, make sure to give the email id you have configured in jenkins.
3. Replace the checout repo and generate the link using Pipeline Syntax
4. while generating the github link will will be in private.To get the link add credentials on the link 
### Major things that you wnat to edit in this project .Goto `fitness/settings.py` on `ALLOWED_HOSTS` provide your `public-ip` of docker instance and save it. And build it.


```groovy
pipeline {
    agent any
    tools {
        jdk 'jdk17'
    }
    environment{
        SCANNER_HOME=tool 'sonar-scanner'
    }
    stages {
        stage('clean Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('git checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/didin2003/lapzone.git'
            }
        }
        stage('sonarqube analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=lapzone -Dsonar.projectKey=lapzone'
                }
            }
        }
        stage('quality gate') {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }
        stage('TRIVY FS SCAN') {
            steps {
                sh "trivy fs . > trivyfs.txt"
            }
        }
        stage('Docker Build and Tag') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        sh 'docker build -t lapzone .'
                        sh 'docker tag lapzone didin8080/lapzone:latest'
                    }
                }
            }
        }
        stage('Push to dockerhub') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        sh 'docker push didin8080/lapzone:latest'
                    }
                }
            }
        }
        stage('Docker scout image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        sh 'docker-scout quickview didin8080/lapzone:latest'
                        sh 'docker-scout cves didin8080/lapzone:latest'
                        sh 'docker-scout recommendations didin8080/lapzone:latest'
                    }
                }
            }
        }
        stage('TRIVY Image SCAN') {
            steps {
                sh "trivy image didin8080/lapzone:latest > trivyfs.txt"
            }
        }
        stage('Deploy to Container') {
            steps {
                sh 'docker run -d --name lapzone -p 8000:8000 didin8080/lapzone:latest'
            }
        }
    }
    post {
    always {
        emailext attachLog: true,
            subject: "'${currentBuild.result}'",
            body: """
                <html>
                <body>
                    <div style="background-color: #FFA07A; padding: 10px; margin-bottom: 10px;">
                        <p style="color: white; font-weight: bold;">Project: ${env.JOB_NAME}</p>
                    </div>
                    <div style="background-color: #90EE90; padding: 10px; margin-bottom: 10px;">
                        <p style="color: white; font-weight: bold;">Build Number: ${env.BUILD_NUMBER}</p>
                    </div>
                    <div style="background-color: #87CEEB; padding: 10px; margin-bottom: 10px;">
                        <p style="color: white; font-weight: bold;">URL: ${env.BUILD_URL}</p>
                    </div>
                </body>
                </html>
            """,
            to: 'didinpg8080@gmail.com',
            mimeType: 'text/html',
            attachmentsPattern: 'trivy.txt'
        }
    }
}
```

### Build it

If it get success


Your application is deployed in docker to access it. 

`<public-ip:8000>`

<div align="center">
<img src="img/deploy-in-docker.png" alt="Logo" height="500%" width="100%">
<p align="center"> Home Page </p>
</div>


**Application deployed in docker**

## **Phase 2: Moniter the Application**

**Step 1: Launch EC2 (Ubuntu 22.04)**

- Provision an EC2 instance on AWS Name: Monitoring Server, Ubuntu 24.04, t2.large, Select the SG created in the Step 1, EBS: 30GB.
- Connect to the instance using SSH.
- - Allow ports

  - HTTP 80
  - HTTPS 443
  - SSH 22
  - CUSTOM TCP 9090
  - CUSTOM TCP 3000

We will install Grafana, Prometheus, Node Exporter in the above instance and then we will monitor


**Step 2: Update the packages**

```bash
sudo apt-get update
```


**Step 3: Installing Prometheus**

```bash
sudo useradd --system --no-create-home --shell /bin/false prometheus

wget https://github.com/prometheus/prometheus/releases/download/v2.47.1/prometheus-2.47.1.linux-amd64.tar.gz

#Extract Prometheus files, move them, and create directories:
tar -xvf prometheus-2.47.1.linux-amd64.tar.gz

cd prometheus-2.47.1.linux-amd64/

sudo mkdir -p /data /etc/prometheus

sudo mv prometheus promtool /usr/local/bin/

sudo mv consoles/ console_libraries/ /etc/prometheus/


sudo mv prometheus.yml /etc/prometheus/prometheus.yml

#Set ownership for directories:
sudo chown -R prometheus:prometheus /etc/prometheus/ /data/
```

**Create a systemd unit configuration file for Prometheus:**

```bash
sudo vi /etc/systemd/system/prometheus.service
```

**Add the following content to the prometheus.service file:**

```plaintext
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

StartLimitIntervalSec=500
StartLimitBurst=5

[Service]
User=prometheus
Group=prometheus
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/data \
  --web.console.templates=/etc/prometheus/consoles \
  --web.console.libraries=/etc/prometheus/console_libraries \
  --web.listen-address=0.0.0.0:9090 \
  --web.enable-lifecycle

[Install]
WantedBy=multi-user.target
```

**Enable and start Prometheus:**

```bash
sudo systemctl enable prometheus
sudo systemctl start prometheus
```

**Verify Prometheus's status:**

```bash
sudo systemctl status prometheus
```

Press Control+c to come out



Access Prometheus in browser using your server's IP and port 9090:

`http://<your-server-ip>:9090`

Click on 'Status' dropdown ---> Click on 'Targets' ---> You can see 'Prometheus (1/1 up)'



**Step 4: Installing Node Exporter**

```bash
cd 

sudo useradd --system --no-create-home --shell /bin/false node_exporter

wget https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz

#Extract Node Exporter files, move the binary, and clean up:
tar -xvf node_exporter-1.6.1.linux-amd64.tar.gz

sudo mv node_exporter-1.6.1.linux-amd64/node_exporter /usr/local/bin/

rm -rf node_exporter*
```

**Create a systemd unit configuration file for Node Exporter:**

```bash
sudo vi /etc/systemd/system/prometheus.service
```

**Add the following content to the node_exporter.service file:**

```plaintext
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

StartLimitIntervalSec=500
StartLimitBurst=5

[Service]
User=node_exporter
Group=node_exporter
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/node_exporter --collector.logind

[Install]
WantedBy=multi-user.target
```


**Enable and start Node Exporter:**

```bash
sudo systemctl enable node_exporter
sudo systemctl start node_exporter
```

**Verify Node Exporter status:**

```bash
sudo systemctl status node_exporter
```

Press Control+c to come out


**Step 5: Configure Prometheus Plugin Integration**

As of now we created Prometheus service, but we need to add a job in order to fetch the details by node exporter. So for that we need to create 2 jobs, one with 'node exporter' and the other with 'jenkins' as shown below;


Integrate Jenkins with Prometheus to monitor the CI/CD pipeline.

Prometheus Configuration:


**To configure Prometheus to scrape metrics from Node Exporter and Jenkins, you need to modify the prometheus.yml file.**

```bash
cd /etc/prometheus/
ls -l
#You can see the "prometheus.yml" file
sudo vi prometheus.yml
```

**You will see the content and also there is a default job called "Prometheus" Paste the below content at the end of the file;**


```yaml
  - job_name: 'node_exporter'
    static_configs:
      - targets: ['<MonitoringVMip>:9100']

  - job_name: 'jenkins'
    metrics_path: '/prometheus'
    static_configs:
      - targets: ['<your-jenkins-ip>:<your-jenkins-port>']
```

In the above, replace <your-jenkins-ip> and <your-jenkins-port> with the appropriate IPs ----> esc ----> :wq


**Check the validity of the configuration file:**

```bash
promtool check config /etc/prometheus/prometheus.yml
```

You should see "SUCCESS" when you run the above command, it means every configuration made so far is good.

**Reload the Prometheus configuration without restarting:**

```bash
curl -X POST http://localhost:9090/-/reload
```

Access Prometheus in browser (if already opened, just reload the page):


`http://<your-prometheus-ip>:9090/targets`


Open Port number 9100 for Monitoring VM

You should now see "Jenkins (1/1 up)" "node exporter (1/1 up)" and "prometheus (1/1 up)" in the prometheus browser.


**step 6: Install Grafana**

You are currently in /etc/Prometheus path.


```bash
sudo apt-get update

sudo apt-get install -y apt-transport-https software-properties-common

cd

wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -

#You should see OK when executed the above command.
echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list

sudo apt-get update

sudo apt-get -y install grafana

sudo systemctl enable grafana-server

sudo systemctl start grafana-server

sudo systemctl status grafana-server
```

Press control+c to come out


Access grafana in browser using your server's IP and port 3000:

`http://<your-server-ip>:3000`

Default username and password is "admin"

You can Set new password or you can click on "skip now".

Click on "skip now" (If you want you can create the password)


**Step 7: Add Prometheus Data Source:**

To visualize metrics, you need to add a data source. Follow these steps:

- Click on the gear icon (⚙️) in the left sidebar to open the "Configuration" menu.

- Select "Data Sources."

- Click on the "Add data source" button.

- Choose "Prometheus" as the data source type.

- In the "HTTP" section:
  - Set the "URL" to `http://localhost:9090` (assuming Prometheus is running on the same server).
  - Click the "Save & Test" button to ensure the data source is working.

**Step 8: Import a Dashboard:**
 
To make it easier to view metrics, you can import a pre-configured dashboard. Follow these steps:

- Click on the "+" (plus) icon in the left sidebar to open the "Create" menu.

- Select "Dashboard."

- Click on the "Import" dashboard option.

- Enter the dashboard code you want to import (e.g., code 1860,9964).

- Click the "Load" button.

- Select the data source you added (Prometheus) from the dropdown.

- Click on the "Import" button.

You should now have a Grafana dashboard set up to visualize metrics from Prometheus.

Grafana is a powerful tool for creating visualizations and dashboards, and you can further customize it to suit your specific monitoring needs.

That's it! You've successfully installed and set up Grafana to work with Prometheus for monitoring and visualization 



## Deploy Application on Argocd Kubernetes

**Step 1: Launch EC2 (ubuntu 22.04)**

- Provision an EC2 instance on AWS with Ubuntu 22.04,volume 29gb,t3.large. 
- Connect to the instance using SSH.
- Allow ports

  - SMTP 25
  - CUSTOM TCP 3000 - 10000
  - HTTP 80
  - HTTPS 443
  - SSH 22
  - CUSTOM TCP 6443
  - SMTPS 465
  - CUSTOM TCP 30000-32676
  - CUSTOM TCP 8000


**Step 2: create kubectl on local system**

```bash
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
kubectl version --short --client
```

**Step 3: create awscli on local system**

```bash
sudo apt install unzip
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws --version
```

**Step 4: create eksctl on local system**

```bash
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version
```

**Step 5: Aws Configure** 

```bash
aws configure
```

Provide Aws Access id, Password, Region, Format=Json


**Step 6: create Kubernetes Cluster** 

```bash
eksctl create cluster --name lapzone-cluster --region eu-north-1 --zones eu-north-1b,eu-north-1c --without-nodegroup
```

It will take atleast 20-25 minutes for the cluster to create.


**Step 7: Create & Associate IAM OIDC Provider for our EKS Cluster**

```bash
eksctl utils associate-iam-oidc-provider --region eu-north-1 --cluster lapzone-cluster --approve
```


**step 8: create Node group**

```bash
eksctl create nodegroup --cluster lapzone-cluster --region eu-north-1 --name node2 --node-type t3.medium --nodes 2 --nodes-min 2 --nodes-max 4 --node-volume-size 20 --ssh-access --ssh-public-key sonu --managed --asg-access --external-dns-access --full-ecr-access --appmesh-access --alb-ingress-access
```


**step 9: Install HELM**

```bash
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null

sudo apt-get install apt-transport-https --yes

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list

sudo apt-get update

sudo apt-get install helm
```


**step 10: ArgoCD installation**

```bash
aws eks update-kubeconfig --name lapzone-cluster --region eu-north-1

kubectl create namespace argocd

kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.4.7/manifests/install.yaml

export ARGOCD_SERVER=$(kubectl get svc argocd-server -n argocd -o json | jq --raw-output '.status.loadBalancer.ingress[0].hostname')

kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

kubectl create namespace prometheus-node-exporter

helm install prometheus-node-exporter prometheus-community/prometheus-node-exporter --namespace prometheus-node-exporter

export ARGOCD_SERVER=$(kubectl get svc argocd-server -n argocd -o json | jq --raw-output '.status.loadBalancer.ingress[0].hostname')

echo $ARGOCD_SERVER   #gives an adress copy it and paste it in the new tab. this is the argocd link.(eg: a4f5b673fa5a04274bbb06884d487745-74365602.eu-north-1.elb.amazonaws.com)

export ARGOCD_PWD=$(kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d)

echo $ARGOCD_PWD     #it gives the password copy it and paste it on the argocd page username is "admin" (eg:XefIYWOviBUSfiCO)
```


In ArgoCD page login it.
- Select new app
- Provide application name given in the repo file eg:(gym-app)
- Project name = default
- Sync policy = automatic
- On source = paste the repo link
- Path = (the file contain the deployment.yml file ,service.yml file and node-service.yml file)
- On destination = click on cluster url a link will popup select that link
- Namespace = default
- Create it
- Select the application and sync , enable force and then ok.


while creating the application an error found means.

```bash
git rm -r venv

echo "venv/" >> .gitignore

git add .gitignore

git commit -m "Remove venv and add to .gitignore"

git push
```

Remove the Environment and add to .gitignore and push to git hub

To access it 

`<node-ip:30222>`


`30222` port number that we have mentioned on servive.yaml file 

<div align="center">
<img src="img/ip1.png" alt="Logo" height="500%" width="100%">
<p align="center">Application deployed on kubernetes</p>
</div>

<div align="center">
<img src="img/ip2.png" alt="Logo" height="500%" width="100%">
<p align="center">Application deployed on kubernetes</p>
</div>

**Look at the ip i had deployed it on 2 node group**

### Major things that you wnat to edit in this project .Goto `lapzone/settings.py` on `ALLOWED_HOSTS` provide your `public-ip` of node instance and save it 
### Rebuild the Docker 