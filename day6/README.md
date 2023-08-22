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
