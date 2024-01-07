pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "32"
    }
    
    stages {
        
        stage('Checkout'){
           steps {
                git credentialsId: 'jenkins_cicd', 
                url: 'https://github.com/makresh-dev/cicd_django_todo_e2e',
                branch: 'main'
           }
        }

        stage('Build Docker'){
            steps{
                script{
                    sh '''
                    echo 'Buid Docker Image'
                    docker build -t mknnyk/django_todo_cicd:${BUILD_NUMBER} .
                    '''
                }
            }
        }

        stage('Push the artifacts'){
           steps{
                script{
                    withCredentials([usernamePassword(credentialsId: 'docker_hub', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                    sh '''
                    echo 'Push to Repo'
                    docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
                    docker push mknnyk/django_todo_cicd:${BUILD_NUMBER}
                    '''
            }
                }
            }
        }
        
        stage('Checkout K8S manifest SCM'){
            steps {
                git credentialsId: 'jenkins_cicd', 
                url: 'https://github.com/makresh-dev/cicd_manifest_todo_app.git',
                branch: 'main'
            }
        }
        
        stage('Update K8S manifest & push to Repo'){
            steps {
                script{
                    withCredentials([usernamePassword(credentialsId: 'jenkins_cicd', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh '''
                        cat deploy.yaml
                        sed -i 's/32/'"${BUILD_NUMBER}"'/g' deploy.yaml
                        cat deploy.yaml
                        git add deploy.yaml
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://github.com/makresh-dev/cicd_manifest_todo_app.git HEAD:main

                        if [[ -n $(git status --porcelain deploy.yaml) ]]; then
                        git add deploy.yaml
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git push https://github.com/makresh-dev/cicd_manifest_todo_app.git HEAD:main
                        else
                            echo "No changes to commit."
                        fi
                        '''                        
                    }
                }
            }
        }
    }
}
