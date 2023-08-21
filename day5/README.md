# cloud4c-cicdb4

### in the process of CI -- we are not pushing image to docker hub 

<img src="cip.png">

### jenkins plugins for pushing image to registry (Docker hub)

<img src="dh.png">

### introduction to jenkinsfile concept 

<img src="jf.png">

### jenkinsfile options

<img src="jf1.png">

### concept of jenkins cluster 

<img src="jfcl.png">

### pipeline job 

```
pipeline  {
    agent any // jenkins master will automatically decide where to run jobs
    stages {
        stage('printing message') {
            steps {
                echo 'Hello world this is jenkinsfile'
            }
            
        }
        stage('checking current time') {
            steps {
                sh 'date'
            }
            
        }
        
        stage('checking calendar') {
            steps {
                sh 'cal'
            }
        }
        
    }
}
```
