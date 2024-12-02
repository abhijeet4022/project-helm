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
    }

    stages {
        stage('Set Up Kubernetes Context') {
            description 'This stage updates the kubeconfig for the EKS cluster.'
            steps {
                script {
                    // Updating the kubeconfig for the specified environment's EKS cluster
                    sh "aws eks update-kubeconfig --name ${params.ENV}-roboshop"
                }
            }
        }

        stage('Clone Application and Helm Code') {
            description 'This stage clones the application code and Helm chart repository for deployment.'
            steps {
                script {
                    // Create APP dir and clone the specified application repository using the APPNAME parameter to use variables.
                    dir('APP') {
                        git branch: 'main', url: "https://github.com/abhijeet4022/${params.APPNAME}"
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
                // sh 'helm upgrade -i ${APPNAME} ./CHART  -f APP/helm/${ENV}.yaml'
                sh 'helm upgrade -i roboshop ./CHART'
            }
        }
    }
}
