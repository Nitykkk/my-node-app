pipeline {
    agent any
    environment { 
        DOCKER_TAG = 'uqlutzavr/steproject-2'
    }

    stages {
        stage('Pull the code') {
            steps {
                git branch: 'main', credentialsId: 'token_for_git', url: 'https://github.com/Nitykkk/my-node-app.git'
                sh 'pwd'
                sh 'whoami'
            }
        }
        stage('Build Dockerimage') {
            steps {
                sh 'docker build -t ${DOCKER_TAG} .'
            }
        }
        stage('Run tests and app') {
            steps {
                script {
                    def statusCode = sh(script: 'docker run --rm ${DOCKER_TAG} test', returnStatus: true)
                    println(statusCode)
                    if (statusCode == 0) {
                        sh 'docker run -d -p 80:80 ${DOCKER_TAG}'
                    } 
                    else {
                        error("Tests failed")
                    }
                }
            }
        }
        stage('Push to dockerhub') {
            when {
                expression { currentBuild.currentResult == 'SUCCESS' }
            }
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-token') {
                        docker.image(DOCKER_TAG).push()
                    }
                }
            }
        }
    }
    
    post {
        failure {
            echo 'Build or tests failed'
        }
    }
}
