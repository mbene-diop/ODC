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
                    sh '/chemin/vers/sonar-scanner -Dsonar.projectKey=mben -Dsonar.sources=. -Dsonar.projectName="ODC" -Dsonar.sourceEncoding=UTF-8'
                }
            }
        }
        */

        stage('Build des images') {
            steps {
                sh 'docker build -t $BACKEND_IMAGE:latest ./Backend-main/odc'
                sh 'docker build -t $FRONTEND_IMAGE:latest ./Frontend-main'
                sh 'docker build -t $MIGRATE_IMAGE:latest ./Backend-main/odc'
            }
        }

        stage('Push des images sur Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'soufa', url: ""]) {
                    sh 'docker push $BACKEND_IMAGE:latest'
                    sh 'docker push $FRONTEND_IMAGE:latest'
                    sh 'docker push $MIGRATE_IMAGE:latest'
                }
            }
        }

        stage('Déploiement local avec Docker Compose') {
            steps {
                sh '''
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
