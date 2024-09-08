pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
	DOCKER_USERNAME = 'bimalrajsharma07'
	DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'
    }
    
    stages {
        
        stage('Checkout'){
           steps {
                git credentialsId: 'b085035d-9661-4890-8c04-2d41743fa83a', 
                url: 'https://github.com/bimal-root/cicd-end-to-end.git',
                branch: 'main'
           }
        }

	stage('Login Docker Hub') {
	    steps{
		script{
		    sh '''
		    echo 'Docker Hub Login'
		    echo "$DOCKER_CREDENTIALS_ID" | docker login -u "$DOCKER_USERNAME" --password-stdn
		    '''
}
}
}

        stage('Build Docker'){
            steps{
                script{
                    sh '''
		    echo 'Buid Docker Image'
                    docker build -t bimalrajsharma07/todoapp:v${BUILD_NUMBER} .
                    '''
                }
            }
        }

        stage('Push the artifacts'){
           steps{
                script{
                    sh '''
                    echo 'Push to Repo'
                    docker push bimalrajsharma07/todoapp:v${BUILD_NUMBER}
                    '''
                }
            }
        }
        
        stage('Checkout K8S manifest SCM'){
            steps {
                git credentialsId: 'b085035d-9661-4890-8c04-2d41743fa83a', 
                url: 'https://github.com/bimal-root/cicd-end-to-end/k8s_Deploy.git',
                branch: 'main'
            }
        }
        
        stage('Update K8S manifest & push to Repo'){
            steps {
                script{
                    withCredentials([usernamePassword(credentialsId: 'b085035d-9661-4890-8c04-2d41743fa83a', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh '''
                        cat deploy.yaml
                        sed -i '' "s/v2/v${BUILD_NUMBER}/g" deploy.yaml
                        cat deploy.yaml
                        git add deploy.yaml
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://github.com/bimal-root/cicd-end-to-end/k8s_Deploy.git HEAD:main
                        '''                        
                    }
                }
            }
        }
    }
}
