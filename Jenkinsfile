pipeline {

        agent any

        environment {
                image = "jonyward/final:${env.BUILD_NUMBER}"
        }
        stages{
                stage('Build docker image') {
                        steps{
                                echo 'Building the application'
                                sh "docker build . -t " + image
                        }
                }

                stage('Push to dockerhub') {
                        steps{
                                echo 'Pushing image to dockerhub'
                                withDockerRegistry([ credentialsId: "Dockerhub", url: "" ]){
                                        sh "docker push " + image
                                }
                        }
                }

                stage('Build container') {
                        steps{
                                echo 'Running the container'
                                sh "docker run --rm --name plswork -p 80:80 -d " + image
                        }
                }

                stage('Deploy to K8s') {
                        steps{ 
                                sshagent(credentials: ['ubuntu']) {
                                                script{
                                                        try{
                                                                sh "ssh -o StrictHostKeyChecking=no ubuntu@54.226.45.223 kubectl set image deployments/kube-server final=" + image
                                                        }
                                                        catch(error){
                                                                }
                                                }
                                }
                        }
                }
        }
}
