pipeline {
    agent any

    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', credentialsId: 'gitID', url: 'https://github.com/harshalbhope/Jenkins_Argo_K8s'
            }
        }

        stage('Build Docker') {
            steps {
                script {
                    sh '''
                    echo 'Buid Docker Image'
                    docker build -t harshalb/cicd-e2e:${BUILD_NUMBER} .
                    
                    '''
                }
            }
        }

        // stage('Push the artifacts') {
        //     steps {
        //         script {
        //             sh '''
        //             echo 'Push to Repo'
        //             docker push harshalb/cicd-e2e:${BUILD_NUMBER}
        //             '''
        //         }
        //     }
        // }

        stage('Checkout K8S manifest SCM') {
            steps {
                git branch: 'main', credentialsId: 'gitID', url: 'https://github.com/harshalbhope/k8s_manifest'
            }
        }

        stage('Update K8S manifest & push to Repo') {
            steps {
                script {
                    withCredentials([sshUserPrivateKey(credentialsId: 'gitID', keyFileVariable: 'gitID')])  {
                        sh '''
                        cat deploy.yaml
                        sed -i '' "s/32/${BUILD_NUMBER}/g" deploy.yaml
                        cat deploy.yaml
                        git add deploy.yaml
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://github.com/harshalbhope/k8s_manifest.git HEAD:main
                        '''
                    }
                }
            }
        }
    }
}
