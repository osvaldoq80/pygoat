pipeline {
    agent any
    stages {

        stage('Safety SCA Scan') {
            agent {
                docker {
                    image 'python:3.11'
                    args '-u root -v $WORKSPACE:/workspace -w /workspace'
                }
            }
            steps {
                withCredentials([string(credentialsId: 'safety-api-key', variable: 'API_KEY')]) {
                    script {
                        try {
                            sh '''
                                echo "=== Instalando Safety ==="
                                pip install --quiet safety

                                echo "=== Ejecutando escaneo de dependencias ==="
                                set +e
                                safety --key $API_KEY --stage cicd scan --output json > safety-report.json 2>&1
                                STATUS=$?
                                set -e

                                echo "Código de salida de Safety: $STATUS"
                                if [ "$STATUS" -eq 0 ]; then
                                    echo "No se detectaron vulnerabilidades."
                                elif [ "$STATUS" -eq 1 ]; then
                                    echo "Vulnerabilidades encontradas. Revisa el reporte safety-report.json"
                                elif [ "$STATUS" -eq 64 ]; then
                                    echo "Error: No se encontró requirements.txt o falló la configuración."
                                else
                                    echo "Error inesperado (exit code $STATUS)."
                                fi

                                # Permitir que el pipeline continúe
                                exit 0
                            '''
                        } catch (err) {
                            echo "Error ejecutando Safety: ${err}"
                            currentBuild.result = 'FAILURE'
                        } finally {
                            echo "Guardando reporte (si existe)..."
                            sh 'ls -lh safety-report.json || echo "No se generó safety-report.json"'
                            archiveArtifacts artifacts: 'safety-report.json', fingerprint: true, allowEmptyArchive: true
                        }
                    }
                }
            }
        }

        stage('Compilation') {
            agent { docker { image 'php:8.2-cli' } }
            steps { sh 'echo "Compilando..."' }
        }

        stage('Build') {
            agent { docker { image 'php:8.2-cli' } }
            steps { sh 'echo "docker build -t my-php-app ."' }
        }

        stage('Deploy') {
            agent { docker { image 'php:8.2-cli' } }
            steps { sh 'echo "docker run my-php-app ."' }
        }
    }

    post {
        always {
            echo "Post: Verificando archivo de reporte..."
            sh 'ls -lh safety-report.json || echo "Reporte no encontrado"'
            archiveArtifacts artifacts: 'safety-report.json', fingerprint: true, allowEmptyArchive: true
        }
    }
}
