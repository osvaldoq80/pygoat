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
				withCredentials([string(credentialsId: 'SAFETY_API_KEY', variable: 'API_KEY')]) {
					sh '''
						pip install safety
						set -e
						safety --key $API_KEY --stage cicd scan --output json > safety-report.json
						STATUS=$?
						set +e

						# Falla el build si hubo vulnerabilidades
						if [ "$STATUS" -ne 0 ]; then
							echo "Safety encontró vulnerabilidades. Revisa safety-report.json"
							exit 1
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
}
