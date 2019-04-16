pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build'
                archiveArtifacts artifacts: 'src/index.html'
            }
        }
        stage('DeployToStage') {
            when {
                branch 'master'
            }
            steps {
                withCredentials([string(credentialsId: 'jenkinsuser', variable: 'USERPASS')]) {
                    sshPublisher(
                        failOnError: true,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'staging',
                                sshCredentials: [
                                    username: 'jenkinsuser',
                                    encryptedPassphrase: "$USERPASS"
                                ], 
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'src/**',
                                        removePrefix: 'src/'
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }
        stage('DeployToProd') {
            when {
                branch 'master'
            }
            steps {
                input 'Does the staging environment look OK?'
                milestone(1)
                withCredentials([string(credentialsId: 'jenkinsuser', variable: 'USERPASS')]) {
                    sshPublisher(
                        failOnError: true,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'production',
                                sshCredentials: [
                                    username: 'jenkinsuser',
                                    encryptedPassphrase: "$USERPASS"
                                ], 
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'src/**',
                                        removePrefix: 'src/'
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }
    }
}

