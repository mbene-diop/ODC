pipeline {
    agent any

    environment {
        DOCKER_USER = 'nayoh'
        BACKEND_IMAGE = "${DOCKER_USER}/odc_docker-backend"
        FRONTEND_IMAGE = "${DOCKER_USER}/odc_docker-frontend"
        MIGRATE_IMAGE = "${DOCKER_USER}/odc_docker-migrate"
    }

    stages {
        stage('Cloner le dépôt') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/mbene-diop/ODC.git'
            }
        }
/*
        stage('Analyse SonarQube') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    bat '"C:\\Users\\hp\\Desktop\\sonar-scanner\\bin\\sonar-scanner.bat" -Dsonar.projectKey=mben -Dsonar.sources=. -Dsonar.projectName="ODC" -Dsonar.sourceEncoding=UTF-8'

                }
            }
        }
*/
        stage('Build des images') {
            steps {
                bat 'docker build -t %BACKEND_IMAGE%:latest ./Backend-main/odc'
                bat 'docker build -t %FRONTEND_IMAGE%:latest ./Frontend-main'
                bat 'docker build -t %MIGRATE_IMAGE%:latest ./Backend-main/odc'
            }
        }

        stage('Push des images sur Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'soufa', url: ""]) {
                    bat 'docker push %BACKEND_IMAGE%:latest'
                    bat 'docker push %FRONTEND_IMAGE%:latest'
                    bat 'docker push %MIGRATE_IMAGE%:latest'
                }
            }
        }

        stage('Déploiement local avec Docker Compose') {
            steps {
                bat '''
                    docker rm -f BackendCont || true
                    docker rm -f FrontendCont || true
                    docker-compose down || true
                    docker-compose pull
                    docker-compose up -d --build
                '''
            }
        }
    }

    /*
    post {
        success {
            mail to: 'nayoh.diop@gmail.com',
                 subject: "réussite",
                 body: "L'application a été déployée."
        }
        failure {
            mail to: 'nayoh.diop@gmail.com',
                 subject: "❌ Échec",
                 body: "Une erreur s’est produite"
        }
    }
    */
}
