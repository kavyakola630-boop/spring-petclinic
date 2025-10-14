pipeline {
    agent {
        label 'JDKJAVA'
    }

    triggers {
        pollSCM('* * * * *')
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/kavyakola630-boop/spring-petclinic.git'
            }
        }

        stage('Java Build and Sonar Scan') {
            steps {
                withCredentials([string(credentialsId: 'sonarqubejava', variable: 'SONAR_TOKEN')]) {
                    withSonarQubeEnv('SONARQUBE') {
                        sh '''
                            mvn clean package sonar:sonar \
                                -Dsonar.projectKey=kavyakola630-boop_spring-petclinic \
                                -Dsonar.organization=jenkinsjava1 \
                                -Dsonar.host.url=https://sonarcloud.io \
                                -Dsonar.login=$SONAR_TOKEN
                        '''
                    }
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            junit 'target/surefire-reports/*.xml'
        }
        success {
            echo '✅ Build and SonarQube analysis successful — Good Pipeline!'
        }
        failure {
            echo '❌ Build failed — Check logs and fix the errors.'
        }
    }
}
