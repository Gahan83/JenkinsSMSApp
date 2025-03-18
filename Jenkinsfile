pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                script {
                    // Restoring dependencies
                    sh "dotnet restore"

                    // Building the application
                    sh "dotnet build --configuration Release"
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Running tests
                    sh "dotnet test --no-restore --configuration Release"
                }
            }
        }

        stage('Publish') {
            steps {
                script {
                    // Publishing the application
                    sh "dotnet publish --no-restore --configuration Release --output .\\publish"
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Deploying the application
                    withCredentials([usernamePassword(credentialsId: 'ec2-credentials', passwordVariable: 'CREDENTIAL_PASSWORD', usernameVariable: 'CREDENTIAL_USERNAME')]) {
                powershell '''
                $credentials = New-Object System.Management.Automation.PSCredential($env:CREDENTIAL_USERNAME, (ConvertTo-SecureString $env:CREDENTIAL_PASSWORD -AsPlainText -Force))

                # Map the EC2 shared drive
                New-PSDrive -Name X -PSProvider FileSystem -Root "\\\\ec2-44-220-164-163.compute-1.amazonaws.com\\coreapp" -Persist -Credential $credentials

                # Copy files to EC2 IIS directory
                Copy-Item -Path '.\\publish\\*' -Destination 'X:\' -Recurse -Force

                # Remove mapped drive after deployment
                Remove-PSDrive -Name X
                '''
                    }
                }
            }
        }
    }
}
