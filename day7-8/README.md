# cloud4c-cicdb4
### implement security in jenkins pipeline 

<img src="jp.png">

### adding security scaning prebuild using docker -- trufflehog 

```
pipeline {
    agent {
        label 'mymaster'
    } // any node can build job either master or slave 

    stages {
        stage('taking code from github and verify it') {
            steps {
                echo 'cloning code from github' // echo is predefine keyword
                git branch: 'master',
                    url: 'https://github.com/redashu/ashu-webapp-project.git' // taking source code 
                // verify all the data 
                sh 'ls -a'
            }
        }
        // SAST tool trufflehog for scanning code 
        stage('prebuild security scanning'){
            agent {
                label 'slave1'
            }
            steps {
                echo 'using docker based trufflehog to scan code'
                sh 'docker run --rm trufflesecurity/trufflehog:latest   github --repo https://github.com/redashu/ashu-webapp-project.git >/tmp/ashu1.txt'
                sh 'cat /tmp/ashu1.txt | grep -Eiw "private|key" && exit 1'
            }
        }
        // use docker-compose for build and run purpsoe 
        stage('using docker compose'){
            steps {
                echo 'using docker compose to manage'
                sh 'docker-compose down'
                sh 'docker-compose up -d --build'
                sh 'docker-compose ps'
                sh 'docker-compose images'
            }
        }
        // verify container status and check health page
        stage('using testing'){
            steps {
                echo 'checking container running status'
                sh 'docker ps | grep -w Up | grep -w ashu-web-c2'
                sh 'curl -f http://localhost:1235/health.html'
            }
        }
        stage('jenkinsfile way to build docker image'){
            steps {
                echo 'using jenkinsfile method'
                script {
                    def ashuImg = "dockerashu/ashu-webui-cloud4c" // creating variables to store data
                    def ashuTag = "version$BUILD_NUMBER"
                    // using docker pluing to build image
                    docker.build(ashuImg + ":" +ashuTag, ".")
                }
                // verify image build
                sh 'docker images | grep ashu-webui-cloud4c'
            }
        }
        stage('pushing image to dockerhub'){
            steps {
                echo 'using jenkins plugins to push image'
                script {
                    def ashuImg = "dockerashu/ashu-webui-cloud4c"
                    def ashuTag = "version$BUILD_NUMBER"
                    def ashuCred = "6b9dcaf6-74ca-45d9-a0ca-f0a4108d816a"
                    // login to docker hub
                    docker.withRegistry('https://registry.hub.docker.com',ashuCred){
                        // pushing image 
                        docker.image(ashuImg + ":" + ashuTag).push()
                    }
                }
            }
        }
    }
}

```

### adding post build action 

```
pipeline {
    agent {
        label 'mymaster'
    } // any node can build job either master or slave 

    stages {
        stage('taking code from github and verify it') {
            steps {
                echo 'cloning code from github' // echo is predefine keyword
                git branch: 'master',
                    url: 'https://github.com/redashu/ashu-webapp-project.git' // taking source code 
                // verify all the data 
                sh 'ls -a'
            }
        }
        // SAST tool trufflehog for scanning code 
        stage('prebuild security scanning'){
            agent {
                label 'slave1'
            }
            steps {
                echo 'using docker based trufflehog to scan code'
                sh 'docker run --rm trufflesecurity/trufflehog:latest   github --repo https://github.com/redashu/ashu-webapp-project.git >/tmp/ashu1.txt'
                sh 'cat /tmp/ashu1.txt | grep -Eiw "private|key" && exit 1'
            }
        }
        // use docker-compose for build and run purpsoe 
        stage('using docker compose'){
            steps {
                echo 'using docker compose to manage'
                sh 'docker-compose down'
                sh 'docker-compose up -d --build'
                sh 'docker-compose ps'
                sh 'docker-compose images'
            }
        }
        // verify container status and check health page
        stage('using testing'){
            steps {
                echo 'checking container running status'
                sh 'docker ps | grep -w Up | grep -w ashu-web-c2'
                sh 'curl -f http://localhost:1235/health.html'
            }
        }
        stage('jenkinsfile way to build docker image'){
            steps {
                echo 'using jenkinsfile method'
                script {
                    def ashuImg = "dockerashu/ashu-webui-cloud4c" // creating variables to store data
                    def ashuTag = "version$BUILD_NUMBER"
                    // using docker pluing to build image
                    docker.build(ashuImg + ":" +ashuTag, ".")
                }
                // verify image build
                sh 'docker images | grep ashu-webui-cloud4c'
            }
        }
        stage('pushing image to dockerhub'){
            steps {
                echo 'using jenkins plugins to push image'
                script {
                    def ashuImg = "dockerashu/ashu-webui-cloud4c"
                    def ashuTag = "version$BUILD_NUMBER"
                    def ashuCred = "6b9dcaf6-74ca-45d9-a0ca-f0a4108d816a"
                    // login to docker hub
                    docker.withRegistry('https://registry.hub.docker.com',ashuCred){
                        // pushing image 
                        docker.image(ashuImg + ":" + ashuTag).push()
                    }
                }
            }
        }
    }
    // adding post build action section 
    post {
        failure {
            echo 'a message to security team to fix the issue in Github Repo'
            
        }
    }
}

```

### jenkins integration with k8s

```
[root@ip-172-31-7-110 ~]# curl -LO https://dl.k8s.io/release/v1.28.0/bin/linux/amd64/kubectl
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   138  100   138    0     0   1829      0 --:--:-- --:--:-- --:--:--  1840
100 47.5M  100 47.5M    0     0  65.7M      0 --:--:-- --:--:-- --:--:-- 40.6M
[root@ip-172-31-7-110 ~]# 
[root@ip-172-31-7-110 ~]# ls
kubectl
[root@ip-172-31-7-110 ~]# mv kubectl  /usr/bin/
[root@ip-172-31-7-110 ~]# chmod  +x /usr/bin/kubectl 
[root@ip-172-31-7-110 ~]# 
[root@ip-172-31-7-110 ~]# su -s /bin-bash: chsh: command not found
^C
[root@ip-172-31-7-110 ~]# su -s /bin/bash jenkins
bash-4.2$ 
bash-4.2$ cd
bash-4.2$ 
bash-4.2$ pwd
/var/lib/jenkins
bash-4.2$ mkdir .kube
bash-4.2$ cd .kube/
bash-4.2$ ls
bash-4.2$ wget 15.207.253.127/admin.conf
--2023-08-24 12:53:30--  http://15.207.253.127/admin.conf
Connecting to 15.207.253.127:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 5645 (5.5K) [text/plain]
Saving to: 'admin.conf'

100%[===================================================================================================================>] 5,645       --.-K/s   in 0s      

2023-08-24 12:53:30 (328 MB/s) - 'admin.conf' saved [5645/5645]

bash-4.2$ ls
admin.conf
bash-4.2$ mv admin.conf  config

```

### jenkinsfile 

```
pipeline {
    agent {
        label 'mymaster'
    } // any node can build job either master or slave 

    stages {
        stage('taking code from github and verify it') {
            steps {
                echo 'cloning code from github' // echo is predefine keyword
                git branch: 'master',
                    url: 'https://github.com/redashu/ashu-webapp-project.git' // taking source code 
                // verify all the data 
                sh 'ls -a'
            }
        }
        // SAST tool trufflehog for scanning code 
        stage('prebuild security scanning'){
            agent {
                label 'slave1'
            }
            steps {
                echo 'using docker based trufflehog to scan code'
                sh 'docker run --rm trufflesecurity/trufflehog:latest   github --repo https://github.com/redashu/ashu-webapp-project.git >/tmp/ashu1.txt'
                sh 'cat /tmp/ashu1.txt | grep -Eiw "private|key"'
            }
        }
        // use docker-compose for build and run purpsoe 
        stage('using docker compose'){
            steps {
                echo 'using docker compose to manage'
                sh 'docker-compose down'
                sh 'docker-compose up -d --build'
                sh 'docker-compose ps'
                sh 'docker-compose images'
            }
        }
        // verify container status and check health page
        stage('using testing'){
            steps {
                echo 'checking container running status'
                sh 'docker ps | grep -w Up | grep -w ashu-web-c2'
                sh 'curl -f http://localhost:1235/health.html'
            }
        }
        stage('jenkinsfile way to build docker image'){
            steps {
                echo 'using jenkinsfile method'
                script {
                    def ashuImg = "dockerashu/ashu-webui-cloud4c" // creating variables to store data
                    def ashuTag = "version$BUILD_NUMBER"
                    // using docker pluing to build image
                    docker.build(ashuImg + ":" +ashuTag, ".")
                }
                // verify image build
                sh 'docker images | grep ashu-webui-cloud4c'
            }
        }
        // add postbuild scan 
        stage('pushing image to dockerhub'){
            steps {
                echo 'using jenkins plugins to push image'
                script {
                    def ashuImg = "dockerashu/ashu-webui-cloud4c"
                    def ashuTag = "version$BUILD_NUMBER"
                    def ashuCred = "6b9dcaf6-74ca-45d9-a0ca-f0a4108d816a"
                    // login to docker hub
                    docker.withRegistry('https://registry.hub.docker.com',ashuCred){
                        // pushing image 
                        docker.image(ashuImg + ":" + ashuTag).push()
                    }
                }
            }
        }
        // testing connection with k8s 
        stage('kubectl apiserver request sending'){
            agent {
                label 'mymaster'
            }
            steps {
                echo 'Sending ApiRequest to k8s control plane using kubectl'
                sh 'kubectl get nodes'
                git branch: 'master',
                    url: 'https://github.com/redashu/ashu-webapp-project.git'
                sh 'kubectl apply -f jk-k8sdeploy/ns.yaml'
                sh 'kubectl apply -f jk-k8sdeploy/'
                sh 'kubectl get deploy,pod,svc,ep -n ashu-space'
            }
        }
    }
    // adding post build action section 
    post {
        failure {
            echo 'a message to security team to fix the issue in Github Repo'
            
        }
    }
}

```
