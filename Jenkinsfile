pipeline {
    agent any
    tools {
        maven "Maven3"
        jdk "JDK17"
    }
    environment {
        registryCredential = 'ecr:eu-central-1:awscreds'
        imageName = '539247460402.dkr.ecr.eu-central-1.amazonaws.com/vprofileappimg'
        vprofileRegistry = 'https://539247460402.dkr.ecr.eu-central-1.amazonaws.com'
    }
    stages {
        stage('fetch code') {
            steps {
                git branch: 'docker', url: 'https://github.com/hkhcoder/vprofile-project.git'
            }
        }
    
        stage('Build') {
            steps {
               sh 'mvn install -Dskip'
            }
            post {
                success {
                   echo "Archiving Artifact"
                   archiveArtifacts artifacts: '**/*.war'
                }
            }
        }

        stage('Unit Test') {
            steps {
               sh 'mvn test'
            }
        }

        stage('Checkstyle Analysis') {
            steps {
               sh 'mvn checkstyle:checkstyle'
            }
        }
        stage('Build App Image') {
            steps {
                script {
                    dockerImage = docker.build(imageName + ":$BUILD_NUMBER", "./Docker-files/app/multistage/")
                }
            }
        }
        stage('Upload App Image') {
            steps {
                script {
                  docker.withRegistry(vprofileRegistry, registryCredential) {
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                  }
                }
            }
        }
    }
}
   
            