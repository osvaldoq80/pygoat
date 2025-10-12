pipeline {
    agent none

    stages {

        stage('Checkout') {
            agent {
                docker { image 'php:8.2-cli' }
            }
            steps {
                echo '=== Etapa Checkout ==='
                sh 'php --version'
            }
        }

        stage('Compilation') {
            agent {
                docker { image 'php:8.2-cli' }
            }
            steps {
                echo '=== Etapa Compilation ==='
                sh 'echo "Compilando..."'
            }
        }

        stage('Build') {
            agent {
                docker { image 'php:8.2-cli' }
            }
            steps {
                echo '=== Etapa Build ==='
                sh 'echo "docker build -t my-php-app ."'
            }
        }

        stage('SCA - Safety') {
            agent {
                docker {
                    image 'python:3.10-slim'
                    args '-v ${WORKSPACE}:/app -w /app'
                }
            }
            steps {
                echo '=== Etapa SAST - Análisis de dependencias con Safety ==='

                // Clonamos el repositorio PyGoat (si no existe)
                sh 'cd pygoat'

                // Instalamos dependencias y ejecutamos análisis
                sh '''
                    cd pygoat
                    pip install --upgrade pip safety
                    pip install -r requirements.txt || true
                    safety check -r requirements.txt --full-report --json > ../safety-report.json || true
                '''

                // Publicar reporte en Jenkins
                script {
                    archiveArtifacts artifacts: 'safety-report.json', onlyIfSuccessful: false
                }
            }
            post {
                always {
                    echo 'Reporte de vulnerabilidades guardado: safety-report.json'
                }
            }
        }

        stage('Deploy') {
            agent {
                docker { image 'php:8.2-cli' }
            }
            steps {
                echo '=== Etapa Deploy ==='
                sh 'echo "docker run my-php-app ."'
            }
        }
    }
}
