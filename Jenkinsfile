pipeline {
    agent {
        docker {
            image 'mcr.microsoft.com/dotnet/sdk:8.0' 
            args '-v /var/jenkins_home:/var/jenkins_home' 
        }
    }

    environment {
        DOTNET_CLI_HOME = "/var/jenkins_home"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Restore') {
            steps {
                sh 'dotnet restore'
            }
        }

        stage('Build') {
            steps {
                sh 'dotnet build --configuration Release'
            }
        }

        stage('Test') {
            steps {
                sh 'dotnet test --no-restore --configuration Release'
            }
        }

        stage('Publish') {
            steps {
                sh 'dotnet publish --no-restore --configuration Release --output ./publish'
            }
        }
    }

    post {
        success {
            echo 'Build, test, and publish successful!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}
