pipeline {
    agent { 
        label 'JDKJAVA' 
    }

    // Check GitHub every 5 minutes for new commits
    triggers { 
        pollSCM('H/5 * * * *') 
    }

    stages {

        stage('Git Checkout') {
            steps {
                echo '📥 Checking out source code...'
                git branch: 'main', url: 'https://github.com/kavyakola630-boop/spring-petclinic.git'
            }
        }

        stage('Java Build and Sonar Scan') {
            steps {
                echo '⚙️ Starting Maven build and SonarQube scan...'
                withCredentials([string(credentialsId: 'sonar_id', variable: 'SONAR_TOKEN')]) {
                    // Ensure your Jenkins SonarQube installation name = 'SONARQUBE'
                    withSonarQubeEnv('SONARQUBE') {
                        sh '''
                            mvn clean package sonar:sonar \
                                -Dsonar.projectKey=kavyakola630-boop_spring-petclinic \
                                -Dsonar.organization=kavyakola630-boop \
                                -Dsonar.host.url=https://sonarcloud.io \
                                -Dsonar.login=$SONAR_TOKEN
                        '''
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                echo '🧠 Waiting for SonarQube Quality Gate result...'
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        always {
            echo '📦 Archiving build artifacts and test reports...'
            archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
            junit '**/target/surefire-reports/*.xml'
        }

        success {
            echo '✅ Build and SonarQube analysis successful — Great job, Kavya!'
        }

        failure {
            echo '❌ Build failed — Check the logs in Jenkins console output.'
        }
    }
}

