pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS_ID = '3603ff0f-9a52-4286-a5f3-6ce837debd8d'
        DOCKER_IMAGE = 'nitykk/My-image'
    }

    stages {
        stage('Checkout') {
            steps {
                // Витягнути код з репозиторію
                git 'https://github.com/Nitykkk/my-node-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Побудувати Docker-образ
                    docker.build(DOCKER_IMAGE)
                }
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

        stage('Push to Docker Hub') {
            when {
                expression {
                    // Перевірити, чи тести успішні
                    currentBuild.result == null || currentBuild.result == 'SUCCESS'
                }
            }
            steps {
                script {
                    // Увійти до облікового запису Docker Hub та відправити зібраний образ
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        def app = docker.image(DOCKER_IMAGE)
                        app.push("latest")
                    }
                }
            }
        }
    }

    post {
        failure {
            // Якщо тести не пройшли, вивести повідомлення
            echo 'Tests failed'
        }
    }
}
