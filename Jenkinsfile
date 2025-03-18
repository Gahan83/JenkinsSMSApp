pipeline {
    agent any  // Runs on any available agent

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/Gahan83/JenkinsSMSApp.git'  
            }
        }

        stage('Build') {
            steps {
                script {
                    sh 'dotnet build --configuration Release'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    sh 'dotnet test --no-build --configuration Release'
                }
            }
        }

        stage('Publish') {
            steps {
                script {
                    sh 'dotnet publish -c Release -o publish'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
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
