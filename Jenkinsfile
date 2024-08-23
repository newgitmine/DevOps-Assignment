pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // Pull the latest code from the Git repository
                git credentialsId: 'git-credentials-id', url: 'https://github.com/newgitmine/DevOps-Assignment.git'
            }
        }

        stage('Build') {
            steps {
                // Add commands to build your application
                sh 'mvn clean package'
                echo 'Building the application...'
            }
        }

        stage('Test') {
            steps {
                // Add commands to test your application
                sh 'mvn test'
                echo 'Testing the application...'
            }
        }

        stage('Deploy') {
            when {
                // This condition ensures that we only deploy if all tests pass
                expression { currentBuild.resultIsBetterOrEqualTo('SUCCESS') }
            }
            steps {
                // SSH into the Compute Engine instance and deploy the application
                sshPublisher(
                    publishers: [
                        sshPublisherDesc(
                            configName: 'compute-engine-ssh-credentials-id',
                            transfers: [sshTransfer(
                                sourceFiles: 'target/*.jar'
                                remoteDirectory: '/var/www/html', // The deployment directory on the server
                                execCommand: 'sudo systemctl restart nginx'
                            )]
                        )
                    ]
                )
            }
        }
    }
    
    post {
        always {
            // Clean up workspace after pipeline completion
            cleanWs()
        }
    }
}
