pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon --gradle-version=5.1.1'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    app = docker.build("stonemct/train-schedule")
                    app.inside {
                        sh 'echo $(curl localhost:8080)'
                    }
                }
            }
        }
        stage('Push Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
    }
}
/*

        stage('DeployToStaging') {
            when {
                branch 'master'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    sshPublisher(failOnError: true,
                            continueOnError: false,
                            publishers: [sshPublisherDesc(configName: 'staging',
                                    sshCredentials: [username           : "$USERNAME",
                                                     encryptedPassphrase: "$USERPASS"],
                                    transfers: [sshTransfer(sourceFiles: 'dist/trainSchedule.zip',
                                            removePrefix: 'dist/',
                                            remoteDirectory: '/tmp',
                                            execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule')])])
                }
            }
        }
        stage('DeployToProduction') {
            when {
                branch 'master'
            }
            steps {
                input 'Does the staging environment look OK?'
                milestone(1)
                withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    sshPublisher(failOnError: true,
                            continueOnError: false,
                            publishers: [sshPublisherDesc(configName: 'production',
                                    sshCredentials: [username           : "$USERNAME",
                                                     encryptedPassphrase: "$USERPASS"],
                                    transfers: [sshTransfer(sourceFiles: 'dist/trainSchedule.zip',
                                            removePrefix: 'dist/',
                                            remoteDirectory: '/tmp',
                                            execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule')])])
                }
            }
        }

    }
}
*/
