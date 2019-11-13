pipeline {
    // node where the jenkins agent will execute the build
    agent any

    // environment variables & docker build args, defined as pipeline parameters
    environment {
        dockerhub_registry = "jrpamid"
	    local_registry = "localhost:5000"
	    dockerhub_creds = "dockerhub_creds"
 	    redis_ver = "${REDIS_VER}"
	    alpine_ver = "${ALPINE_VER}"
	    centos_ver = "${CENTOS_VER}"
	    ubuntu_codename = "${UBUNTU_CODENAME}"
	    alpine_image = "redis-alpine"
	    centos_image = "redis-centos"
	    ubuntu_image = "redis-ubutu"
	}
    
    //build stages
    stages{

        stage ('1. scm checkout'){
            steps {
              echo "checking out source code from github"  
              git "https://github.com/jrpamid/redis.git"  
            }
        }

        stage ('2. build alpine based redis image'){
            steps {
                echo "****************** Alpine - Redis  Image ************************"
                echo "ALPINE_VERSION = alpine:${ALPINE_VER}, REDIS = redis-${REDIS_VER}"
                sh ''
            }
            post {
                success {
                 echo "Pushing redis-alpine image to " 
                   archiveArtifacts artifacts: 'java-app/target/*.jar', fingerprint: true
                }
            }
        }

        stage ( '3. build centos based redis image'){
            steps {
                echo "CENTOS_VERSION = centos:${CENTOS_VER},  REDIS = redis-${REDIS_VER}"
            }
        }
            
        stage ( '4. build  ubuntu based redis image'){
            steps {
                echo "UBUNTU_VERSION = ubuntu:${UBUNTU_CODENAME}, REDIS = redis-${REDIS_VER}"
            }
        }           
    }

    //port build instructions
    post {
        always {
            echo "This block will be executed always"
        }
        success {
            echo "This  block will be executed on success  of pipeline"
             //mail to: team@example.com, subject: 'Build Success :)'
        }
        failure{
            echo "This block will be executed in case  of pipeline failure"
            //mail to: team@example.com, subject: 'The Pipeline failed :('
        }
        unstable {
            echo "This block  will be executed in case of job stability"
        }
    }
}