pipeline {
    agent {
        docker {
            image 'mcr.microsoft.com/dotnet/sdk:8.0'
            args '-v /var/jenkins_home:/var/jenkins_home'
        }
    }

    environment {
        DOTNET_CLI_HOME = "/var/jenkins_home"
        IIS_HOST = "ec2-44-220-164-163.compute-1.amazonaws.com"
        IIS_USER = "Administrator"
        IIS_PASS = "FhKc@5OoTbHptzV)XdPH4NNRjGQR7nV?"
        PUBLISH_DIR = "./publish"
        REMOTE_DIR = "C:\\inetpub\\wwwroot\\YourApp" // Update as per your IIS setup
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


        stage('Publish') {
            steps {
                sh 'dotnet publish --no-restore --configuration Release --output ${PUBLISH_DIR}'
            }
        }

        stage('Deploy to IIS') {
            steps {
                script {
                    // Create a ZIP file of the published output
                    sh "zip -r app_publish.zip ${PUBLISH_DIR}"

                    // Copy the ZIP file to the remote IIS server
                    sh """
                    sshpass -p '${IIS_PASS}' scp -o StrictHostKeyChecking=no app_publish.zip ${IIS_USER}@${IIS_HOST}:C:\\Windows\\Temp\\app_publish.zip
                    """

                    // Remote PowerShell script to extract and deploy
                    sh """
                    sshpass -p '${IIS_PASS}' ssh -o StrictHostKeyChecking=no ${IIS_USER}@${IIS_HOST} powershell -Command "
                        Expand-Archive -Path C:\\Windows\\Temp\\app_publish.zip -DestinationPath ${REMOTE_DIR} -Force;
                        Remove-Item -Path C:\\Windows\\Temp\\app_publish.zip;
                        Restart-WebAppPool -Name 'DefaultAppPool';
                    "
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Build, test, publish, and deployment successful!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
