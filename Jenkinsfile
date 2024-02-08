pipeline {
stages {
    stage('Build') {
        agent { //here we select only docker build agents
            docker {
                image 'maven:latest' //container will start from this image
                args '-v /root/.m2:/root/.m2' //here you can map local maven repo, this let you to reuse local artifacts
            }
        }
        steps {
            sh 'mvn -B -DskipTests clean package' //this command will be executed inside maven container
        }
    }
    stage('Test') { //on this stage New container will be created, but current pipeline workspace will be remounted to it automatically
        agent {
            docker {
                image 'maven:latest'
                args '-v /root/.m2:/root/.m2'
            }
        }
        steps {
            sh 'mvn test' 
        }
    }
    stage ('Build docker image') { //here you can check how you can build even docker images inside container
        agent {
            docker {
                image 'maven:latest'
                args '-v /root/.m2:/root/.m2 -v /var/run/docker.sock:/var/run/docker.sock' //here we expose docker socket to container. Now we can build docker images in the same way as on host machine where docker daemon is installed
            }
        }
        steps {
            sh 'mvn -Ddocker.skip=false -Ddocker.host=unix:///var/run/docker.sock docker:build' //example of how to build docker image with pom.xml and fabric8 plugin
        }
    }
}
}
