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
