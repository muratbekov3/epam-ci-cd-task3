pipeline {

    agent any

    tools {
        nodejs 'NodeJS-7.8.0'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {

                    if (env.BRANCH_NAME == 'main') {
                        sh 'docker build -t nodemain:v1.0 .'
                    }

                    else if (env.BRANCH_NAME == 'dev') {
                        sh 'docker build -t nodedev:v1.0 .'
                    }

                }
            }
        }

        // stage('Deploy') {
        //     steps {
        //         script {

        //             if (env.BRANCH_NAME == 'main') {

        //                 sh '''
        //                     docker stop nodemain-container || true
        //                     docker rm nodemain-container || true

        //                     docker run -d \
        //                         --name nodemain-container \
        //                         --expose 3000 \
        //                         -p 3000:3000 \
        //                         nodemain:v1.0
        //                 '''

        //             }

        //             else if (env.BRANCH_NAME == 'dev') {

        //                 sh '''
        //                     docker stop nodedev-container || true
        //                     docker rm nodedev-container || true

        //                     docker run -d \
        //                         --name nodedev-container \
        //                         --expose 3001 \
        //                         -p 3001:3000 \
        //                         nodedev:v1.0
        //                 '''
        //             }
        //         }
        //     }
        // }
    }
}