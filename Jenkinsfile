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
        stage('Compile with Maven') {
            steps {
                sh 'mvn compile'
            }
        }
   }
}