pipeline {
    agent any
    environment {
        KUBE_CONFIG = "--kubeconfig=/var/jenkins_home/.kube/config"
    }

    stages {
        stage('Deploy Microservices') {
            steps {
                script {
                    def microservices = sh(script: "ls -d */ | sed 's:/*\$::'", returnStdout: true).trim().split("\n")

                    microservices.each { microservice ->
                        dir(microservice) {
                            echo "Deploying ${microservice}..."
                            sh """
                                kubectl ${KUBE_CONFIG} apply -f deployment.yaml
                                kubectl ${KUBE_CONFIG} apply -f service.yaml
                            """
                        }
                    }
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    def microservices = sh(script: "ls -d */ | sed 's:/*\$::'", returnStdout: true).trim().split("\n")

                    microservices.each { microservice ->
                        echo "Verifying deployment for ${microservice}..."
                        sh "kubectl ${KUBE_CONFIG} get pods -l app=${microservice}"
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished'
        }
        success {
            echo 'Deployment was successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
