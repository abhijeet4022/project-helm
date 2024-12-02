pipeline {
    agent any

    options {
        ansiColor('xterm')
        buildDiscarder(logRotator(
            numToKeepStr: '4', // Keep only the last 4 builds
            artifactNumToKeepStr: '4' // Keep artifacts of only the last 4 builds
        ))
    }

    parameters {
        string(name: 'ENV', defaultValue: 'dev', description: 'Specify the target environment for deployment.')
        string(name: 'APPNAME', defaultValue: '', description: 'Specify the name of the component to deploy.')
        string(name: 'App_Version', defaultValue: '', description: 'Specify the App_Version of the component to deploy.')
    }

    environment {
            BADGE_NAME = "Helm Deployment" // Name of the badge
    }

    stages {
        stage('Set Build Badge') {
            steps {
                script {
                    currentBuild.description = "${BADGE_NAME}: In Progress 🔵"
                }
            }
        }

        stage('Set Up Kubernetes Context') {
            steps {
                script {
                    // Updating the kubeconfig for the specified environment's EKS cluster
                    sh "aws eks update-kubeconfig --name ${params.ENV}-roboshop"
                }
            }
        }

        stage('Clone Application and Helm Code') {
            steps {
                script {
                    // Create APP dir and clone the specified application repository using the APPNAME parameter to use variables.
                    dir('APP') {
                        git branch: 'main', url: "https://github.com/abhijeet4022/${params.APPNAME}-v1"
                    }
                    // Create CHART dir and clone the Helm chart repository for deployment.
                    dir('CHART') {
                        git branch: 'main', url: 'https://github.com/abhijeet4022/project-helm'
                    }
                }
            }
        }

        stage('Helm Deploy'){
            steps{
                sh 'helm upgrade -i ${APPNAME} ./CHART -f APP/helm/${ENV}.yaml --set App_Version=${App_Version}'
            }
        }
    }

    post {
        success {
            script {
                currentBuild.description = "${BADGE_NAME}: Success ✅"
            }
        }
        failure {
            script {
                currentBuild.description = "${BADGE_NAME}: Failed ❌"
            }
        }
        always {
            cleanWs() // Clean workspace after every build
        }
    }


}
