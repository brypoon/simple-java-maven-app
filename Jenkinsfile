pipeline {
    agent any
    tools { 
        maven 'Maven_3.8.4' 
        jdk 'openjdk_11' 
    }
    stages {
        stage ('Initialize') {
            steps {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                ''' 
            }
        }
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
            }
        }
        stage('DeployToServer') {
            steps {
                sshPublisher(
                    failOnError: true,
                    continueOnError: false,
                    publishers: [
                        sshPublisherDesc(configName: 'tomcat',
                        transfers: [
                            sshTransfer(
                                cleanRemote: false,
                                excludes: '',
                                execCommand: 'sudo systemctl stop tomcat && rm -rf /opt/tomcat/latest/webapps/sample/* && unzip /tmp/sample.zip -d /opt/tomcat/latest/webapps && sudo systemctl start tomcat', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '/tmp', remoteDirectorySDF: false, removePrefix: 'dist/', sourceFiles: 'dist/sample.zip')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
            }
        }
    }
}
