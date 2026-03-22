pipeline {
    agent any

    triggers {
        githubPush()
    }

    stages {

        stage('clone code') {
            steps {
                git url:"https://github.com/mohiturkudeaws/flask-app-ecs.git", branch:"main"
            }
        }

        stage('build image') {
            steps {
                sh "docker build -t python-app:${BUILD_NUMBER} ."
            }
        }

        stage('deploy') {
            steps {
                sh """
                docker stop python-container || true
                docker rm python-container || true
                docker run -d -p 80:80 --name python-container python-app:${BUILD_NUMBER}
                """
            }
        }

        stage('verify') {
            steps {
                sh "curl -f http://localhost || exit 1"
            }
        }
    }

    post {
        always {
            mail to: 'chetanurkude27@gmail.com',
            subject: "Build Result: ${currentBuild.currentResult}",
            body: """
            Build Status: ${currentBuild.currentResult}
            Job: ${env.JOB_NAME}
            Build: ${env.BUILD_NUMBER}
            URL: ${env.BUILD_URL}
            """
        }
    }
}
