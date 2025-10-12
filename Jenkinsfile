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
					# Instalar dependencias del sistema necesarias
					apt-get update -y && apt-get install -y gcc libpq-dev build-essential python3-dev

					# Crear entorno virtual dentro del contenedor
					python -m venv venv
					. venv/bin/activate

					# Actualizar pip e instalar Safety
					pip install --upgrade pip
					pip install safety

					# Instalar dependencias del proyecto PyGoat
					pip install -r requirements.txt

					# Ejecutar análisis con Safety (usando API key de Jenkins)
					safety scan --key=$SAFETY_API_KEY --json > safety-report.json || true
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
