# cloud4c-cicdb4

### Using jenkins file to build 3 stages

## Jenkinsfile 

### Single stage jenkinsfile 

```
pipeline {
    agent  any // any node can build job either master or slave 

    stages {
        stage('taking code from github and verify it') {
            steps {
                echo 'cloning code from github' // echo is predefine keyword
                git 'https://github.com/redashu/ashu-webapp-project.git' // taking source code 
                // verify all the data 
                sh 'ls -a'
            }
        }
    }
}

```

### build file 

```
pipeline {
    agent  any // any node can build job either master or slave 

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
    }
}

```

### jenkinsfile with static job on particular node

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
    }
}

```

### adding testing stage 

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
    }
}

```
## we can use jenkinsfile plugin method also to build image

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
                    def ashuImg = "ashu-webui-cloud4c" // creating variables to store data
                    def ashuTag = "version$BUILD_NUMBER"
                    // using docker pluing to build image
                    docker.build(ashuImg + ":" +ashuTag, ".")
                }
                // verify image build
                sh 'docker images | grep ashu-webui-cloud4c'
            }
        }
    }
}

```
