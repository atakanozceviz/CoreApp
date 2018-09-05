pipeline {
    agent any
    stages {
        stage('Git Checkout') {
            steps {
                script {
                    if(env.REF == null){
                        checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/atakanozceviz/CoreApp.git']]])
                    } else {
                        checkout([$class: 'GitSCM', branches: [[name: '$REF']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/atakanozceviz/CoreApp.git']]])
                    }
                    sh 'git clean -fx -d'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    sh 'cd CoreApp.Tests && dotnet test --logger:trx'
                    step([$class: 'MSTestPublisher', testResultsFile:"CoreApp.Tests/TestResults/*.trx", failOnError: true, keepLongStdio: true])
                }
            }
        }
        stage('Build') {
            steps {
                script {
                    sh 'cd CoreApp.Web && sudo docker-compose down && sudo docker build -t coreapp . && sudo docker-compose up -d'
                    sh 'sudo docker system prune --volumes -f'
                }
            }
        }
    }
    post {
        failure {
            emailext attachLog: true, body: "${currentBuild.currentResult}: Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}",
                recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']],
                subject: "Jenkins Build ${currentBuild.currentResult}: Job ${env.JOB_NAME}"
        }

        changed {
            script {
                if(currentBuild.currentResult == 'SUCCESS') {
                    emailext attachLog: true, body: "${currentBuild.currentResult}: Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}",
                    recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']],
                    subject: "Jenkins Build FIXED: Job ${env.JOB_NAME}"
                }
            }
        }
    }
}
