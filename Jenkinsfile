pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo "Running build automation ProjectName: ${env.JOB_NAME}"
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
                    echo "${USER}"

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

                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login'){
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('Deploy to prod'){
            when {
                branch 'master'
            }
            steps{
                withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]){
                    script{
                        sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker pull bulibuli/train-schedule:${env.BUILD_NUMBER}\""
                    try{
                        sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker stop train-schedule\""
                        sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@prod_ip \"docker rm train-schedule\""
                    }
                        catch (err){
                            echo: 'Error: $err'
                        }
                        ssh "sshpass -p $USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@prod_ip \"docker run --restart always --name train-schedule -p 8080:8080 -d bulibuli/train-schedule:${env.BUILD_NUMBER}\""

                    }
                }
            }
        }

    }
}
