pipeline {
    agent any

    environment {
        DOTNET_CLI_HOME = "/var/dotnet"
        PATH = "/usr/share/dotnet:$PATH"  // Ensure dotnet is in PATH
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh '''
                    dotnet --version
                    dotnet restore
                    dotnet build --configuration Release
                '''
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
