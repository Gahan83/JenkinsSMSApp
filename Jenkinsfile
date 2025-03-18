pipeline {
    agent any

    environment {
        EC2_USER = "Administrator"
        EC2_HOST = "ec2-44-220-164-163.compute-1.amazonaws.com"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/Gahan83/JenkinsSMSApp.git'
            }
        }

        stage('Build ASP.NET App') {
            steps {
                script {
                    dir('your-dotnet-app') {
                        sh 'dotnet publish -c Release -o publish'
                    }
                }
            }
        }

        stage('Deploy to IIS') {
            steps {
                script {
                    // Deploy ASP.NET Core app
                    sh "scp -r your-dotnet-app/publish/* ${EC2_USER}@${EC2_HOST}:${APP_PATH}/dotnet"
                }
            }
        }
    }
}
