pipeline {
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git credentialsId: 'b085035d-9661-4890-8c04-2d41743fa83a', 
                    url: 'https://github.com/bimal-root/cicd-end-to-end.git',
                    branch: 'main'
            }
        }

        stage('Build Docker') {
            steps {
                script {
                    sh '''
                    echo 'Build Docker Image'
                    docker build -t bimalrajsharma07/todoapp:v${BUILD_NUMBER} .
                    '''
                }
            }
        }

        stage('Push the artifacts') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh '''
                        echo 'Logging for Docker Push..'
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                        '''
			sh '''
			echo 'Push to Repo'
                        docker push bimalrajsharma07/todoapp:v${BUILD_NUMBER}
                        '''
                    }
                }
            }
        }
        
        stage('Checkout K8S manifest SCM') {
            steps {
                git credentialsId: 'b085035d-9661-4890-8c04-2d41743fa83a', 
                    url: 'https://github.com/bimal-root/cicd-end-to-end.git',
                    branch: 'main'
		
		dir('k8s_Deploy'){
		    sh 'ls -la'
		}
            }
        }
        
        stage('Update K8S manifest & push to Repo') {
            steps {
		dir('k8s_Deploy'){
                script {
                    withCredentials([usernamePassword(credentialsId: 'b085035d-9661-4890-8c04-2d41743fa83a', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh '''
			pwd
                        cat deploy.yaml
			ls -la
   			pwd
			sed -i '' "s/v2/v${BUILD_NUMBER}/g" k8s_Deploy/deploy.yaml
                        cat k8s_Deploy/deploy.yaml
                        git add k8s_Deploy/deploy.yaml
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://github.com/bimal-root/cicd-end-to-end.git HEAD:main
                        '''  
		    	}
                    }
                }
            }
        }
    }
}

