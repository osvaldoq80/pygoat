pipeline { 
    agent any 
    stages  { 
        stage('Safety') {
            agent {
                docker {
                    image 'python:3.11'
                    args '-u root'
                }
            }
            steps {
                withCredentials([string(credentialsId: 'safety-api-key', variable: 'API_KEY')]) {
                    sh '''
                        echo "=== Instalando Safety ==="
                        pip install safety
        
                        echo "=== Ejecutando escaneo ==="
                        set +e
                        safety --key $API_KEY --stage cicd scan --output json > /var/jenkins_home/workspace/safety-report.json
                        STATUS=$?
                        set -e
        
                        if [ "$STATUS" -eq 0 ]; then
                            echo "No se detectaron vulnerabilidades."
                        else
                            echo "Safety encontró vulnerabilidades o errores (exit code $STATUS)."
                        fi
                    '''
                }
            }
        }
		stage('Compilation') { 
			agent { 
				docker { image 'php:8.2-cli' } 
			} 
			steps { 
				sh 'echo "Compilando..."' 
			} 
		} 
		stage('Build') { 
			agent { 
				docker { image 'php:8.2-cli' } 
			} 
			steps { 
				sh 'echo "docker build -t my-php-app ."' 
			} 
		} 
		stage('Deploy') { 
			agent { 
				docker { image 'php:8.2-cli' } 
			} 
			steps { 
				sh 'echo "docker run my-php-app ."'
			} 
		} 
	}
	post {
        always {
            echo "Verificando existencia del reporte..."
            sh 'ls -lh safety-report.json || echo "No se encontró el archivo safety-report.json"'
            archiveArtifacts artifacts: 'safety-report.json', fingerprint: true, allowEmptyArchive: true
        }
    }
}


