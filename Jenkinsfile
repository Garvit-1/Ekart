pipeline {
    agent any
    tools{
        jdk "jdk17"
        maven "maven3"
    }
    environment {
        SCANNER_HOME= tool 'sonar-scanner' 
    }

    stages {
        stage('git checkout') {
            steps {
                git branch: 'main', changelog: false, credentialsId: '85ea045c-2068-4b85-9713-55236fe97490', poll: false, url: 'https://github.com/jaiswaladi246/Ekart'
            }
        }
        stage('compile') {
            steps {
                sh "mvn clean compile -DskipTests=true"
            }
        }
        stage('owasp scan') {
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DP'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml' 
            }
        }
        stage('sonarqube') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Ekart\
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=Ekart '''
                } 
            }
        }
        stage('build') {
            steps {
                sh "mvn clean package -DskipTests=true" 
            }
        }
        stage('docker build and push') {
            steps {
                script{
                    withDockerRegistry(credentialsId: '6c1c3cfa-8836-4a48-b402-b4b7ca5960eb', toolName: 'docker') {
                        // some block
                        sh 'docker build -t ekart -f docker/Dockerfile .'
                        sh 'docker tag ekart garvitgarg/ekart:latest'
                        sh 'docker push garvitgarg/ekart:latest'
                        }
                }
            }
        }
    }
}
