pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build docker image'){
            when {
                branch 'master'
            }
            steps {
                script {
                    echo 'msg'
                    echo $USER

                    app = docker.build("bulibuli/train-schedule:latest")
                    app.inside {
                        sh 'echo $(curl localhost:8080)'
                    }
                }
            }
        }
        stage('Push image to docker') {
            when {
                branch 'master'
            }
            steps{
                script {

                    docker.withRegistry('https://registry.hub.docker.com', 'ca486cc1-5187-4885-a4ce-2b7f694751c6'){
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }

    }
}
