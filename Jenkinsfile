pipeline {
    agent any
        stages {
            stage('Build maven'){
                steps {
                    sh 'pwd'
                    sh 'mvn clean install package'
                }
            }
            stage('Copy Artifact'){
                steps {
                    sh 'pwd'
                    sh 'cp -r target/*.jar docker'
                }
            }
            stage('Run Test'){
                steps{
                    sh 'mvn test'
                }
            }
            stage('Build docker image'){
                steps{
                    script {
                        def customImage = docker.build("sid7100/petclinic:${env.BUILD_NUMBER}", "./docker")
                        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub'){
                        customImage.push()
                        }
                    }
                }
            }
            stage('Build on Kubernetes'){
                steps {
                    withkubeConfig([credentialsId: 'kubeconfig']){
                        sh 'pwd'
                        sh 'cp -R helm/*'
                        sh 'ls -ltrh'
                        sh 'pwd'
                        sh '/usr/local/bin/heml upgrade --install petclinic-app petclinic --set images.repositry=sid7100/petclinic --set image.tag=$(BUILD_NUMBER)'

                    }
                }

            }
        }
}
