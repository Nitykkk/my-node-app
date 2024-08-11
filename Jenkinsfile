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

        stage('Run Tests') {
            steps {
                script {
                    // Запустити Docker-образ з тестами
                    def app = docker.image(DOCKER_IMAGE)
                    app.inside {
                        sh 'Run-test' // Замість 'your-test-command' використовуйте команду для запуску тестів
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
