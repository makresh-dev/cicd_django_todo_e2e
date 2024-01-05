pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        
        stage('Checkout'){
           steps {
                git credentialsId: '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', 
                url: 'https://github.com/makresh-dev/cicd_django_todo_e2e',
                branch: 'main'
           }
        }

        stage('Build Docker'){
            steps{
                script{
                    sh '''
                    echo 'Buid Docker Image'
                    sudo docker build -t mknnyk/django_todo_cicd:${BUILD_NUMBER} .
                    '''
                }
            }
        }

        stage('Push the artifacts'){
           steps{
                script{
                    sh '''
                    echo 'Push to Repo'
                    sudo docker push mknnyk/django_todo_cicd:${BUILD_NUMBER}
                    '''
                }
            }
        }
        
        stage('Checkout K8S manifest SCM'){
            steps {
                git credentialsId: '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', 
                url: 'https://github.com/makresh-dev/cicd-demo-manifests-repo.git',
                branch: 'main'
            }
        }
        
        stage('Update K8S manifest & push to Repo'){
            steps {
                script{
                    withCredentials([usernamePassword(credentialsId: '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', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh '''
                        cat deploy.yaml
                        sed -i '' "s/32/${BUILD_NUMBER}/g" deploy.yaml
                        cat deploy.yaml
                        git add deploy.yaml
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://github.com/makresh-dev/cicd-demo-manifests-repo.git HEAD:main
                        '''                        
                    }
                }
            }
        }
    }
}
