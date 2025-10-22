pipeline {
    agent any
    tools {
        maven 'maven'
    }
    stages {
        stage('Checkout FROM GIT') {
            steps {
                git branch: 'prod' , url: 'https://github.com/bkrrajmali/enahanced-petclinc-springboot.git'
        }
      }
        stage('Validate with Maven ') {
            steps {
                sh 'mvn validate'
            }
        }
        stage('Compile with Maven ') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('Sonar Analysis ') {
            environment {
                SCANNER_HOME = tool 'Sonar-scanner'
            }   
            steps {
                withSonarQubeEnv('sonarserver') {
                    sh '''${SCANNER_HOME}/bin/sonar-scanner \
                    -Dsonar.organization=bkrrajmali \
                    -Dsonar.projectName=springbootjavaapp \
                    -Dsonar.projectKey=springbootjavaapp \
                    -Dsonar.java.binaries=.
                  '''
                }
            }         
        }
    }
}