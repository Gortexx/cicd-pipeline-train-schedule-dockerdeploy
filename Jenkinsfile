pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                
                sh './gradlew --version'
                echo 'Running stacktrace'
                sh './gradlew --stacktrace'
              
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
    }
}
