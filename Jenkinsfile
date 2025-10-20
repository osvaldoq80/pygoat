pipeline {
    agent any

    stages {


        stage('SCA - Safety') {
            agent {
                docker {
                    image 'python:3.11'
                    args '-u root'
                }
            }
            steps {
                script {
                    try {
                        withCredentials([string(credentialsId: 'safety-api-key', variable: 'API_KEY')]) {
                            sh '''
                                echo "===Iniciando análisis SCA con Safety ==="

                                # Validar que el archivo de dependencias exista
                                if [ ! -f requirements.txt ]; then
                                    echo "ERROR: No se encontró el archivo requirements.txt en el directorio actual"
                                    exit 1
                                fi

                                # Instalar Safety
                                echo "Instalando Safety CLI..."
                                pip install --no-cache-dir safety

                                # Ejecutar el escaneo
                                echo "Ejecutando escaneo con Safety..."
                                set +e
                                safety scan --key $API_KEY --file=requirements.txt --output json > safety-report.json 2>&1
                                STATUS=$?
                                set -e

                                # Evaluar resultado del análisis
                                if [ "$STATUS" -eq 0 ]; then
                                    echo "No se detectaron vulnerabilidades."
                                elif [ "$STATUS" -eq 64 ]; then
                                    echo "Safety no pudo analizar el archivo (posiblemente error en dependencias)."
                                else
                                    echo "Safety detectó vulnerabilidades. Revisa el reporte: safety-report.json"
									exit 1
                                fi
                            '''
                        }

                       

                    } catch (err) {
                        echo "Error general durante el escaneo con Safety: ${err}"
                        currentBuild.result = 'FAILURE'
                    } finally {
                        archiveArtifacts artifacts: 'safety-report.json', fingerprint: true, allowEmptyArchive: true
                        echo "Reporte de vulnerabilidades archivado en Jenkins."
                    }
                }
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
