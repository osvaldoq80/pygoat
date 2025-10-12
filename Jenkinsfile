pipeline {
    agent none

    stages {

        stage('Checkout') {
            agent {
                docker { image 'python:3.10-slim' }
            }
            steps {
                echo '=== Etapa Checkout ==='
                sh 'python --version'
            }
        }

        stage('Compilation') {
            agent {
                docker { image 'python:3.10-slim' }
            }
            steps {
                echo '=== Etapa Compilation ==='
                sh 'echo "Compilando proyecto Python..."'
            }
        }

        stage('Build') {
            agent {
                docker { image 'python:3.10-slim' }
            }
            steps {
                echo '=== Etapa Build ==='
                sh 'echo "Construyendo imagen o entorno..."'
            }
        }

        stage('SCA - Safety') {
            agent {
                docker {
                    image 'python:3.10-slim'
                    args '-u root'
                }
            }
            environment {
                SAFETY_API_KEY = credentials('safety-api-key')
            }
            steps {
                echo "=== Etapa SCA - Análisis de dependencias con Safety ==="
                sh '''
                    python -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install safety
                    pip install -r requirements.txt
                    safety scan --key=$SAFETY_API_KEY --json > safety-report.json
                '''
            }
            post {
                always {
                    echo "Reporte de vulnerabilidades guardado: safety-report.json"
                    archiveArtifacts artifacts: 'safety-report.json', allowEmptyArchive: true
                }
            }
        }

        stage('Deploy') {
            agent {
                docker { image 'python:3.10-slim' }
            }
            steps {
                echo '=== Etapa Deploy ==='
                sh 'echo "Desplegando aplicación Python..."'
            }
        }
    }
}
