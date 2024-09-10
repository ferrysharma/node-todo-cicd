pipeline {
    agent any

    environment{

        SONAR_HOME = tool name: 'Sonar', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
    }

    stages {
        
        stage("code"){
            steps{
                git url: "https://github.com/ferrysharma/node-todo-cicd.git", branch: "master"
                echo "bhaiyya code clone ho gaya"
            }
        }
        stage("build and test"){
            steps{
                sh "docker build -t node-app-test-new ."
                echo "code build bhi ho gaya"
            }
        }
        stage("scan image"){
            steps{
                echo "image scanning ho gayi"
            }
        }
        stage("push"){
            steps{
                withCredentials([usernamePassword(credentialsId:"dockerhub_creds", passwordVariable:"dockerHubPass", usernameVariable:"dockerHubUser")]){
                sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                sh "docker tag node-app-test-new:latest ${env.dockerHubUser}/node-app-test-new:latest"
                sh "docker push ${env.dockerHubUser}/node-app-test-new:latest"
                echo "image push ho gaya"
                }
            }
        }
        stage("SonarQube Analysis"){
            steps{
              withSonarQubeEnv("SonarQube-Server"){
                sh "$SONAR_HOME/bin/sonar-scanner -Dsonar.projectName=notetodo -Dsonar.projectKey=note-to-app"
              }
            }
        }
        stage("SonarQube Quality Gates"){
            steps{
              timeout(time: 1, unit: "MINUTES"){
                   waitForQualityGate abortPipeline: false 
              }
            }
        }
        stage("deploy"){
            steps{
                sh "docker compose down && docker compose up -d"
                echo "deployment ho gayi"
            }
        }
    }
}
