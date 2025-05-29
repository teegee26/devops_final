pipeline {
    agent any
    // environment{
    //     MONGO_DB_URL = credentials('MONGO_DB_URL')
    // }
    stages {
        stage('checkout') {
            steps {
                script {
                    // Checkout Frontend Repository
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: 'main']],
                        userRemoteConfigs: [[url: 'https://github.com/timgio26/customerDataPlatform_react.git']],
                        extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'frontend']]
                    ])
                    // Checkout Backend Repository
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: 'main']],
                        userRemoteConfigs: [[url: 'https://github.com/timgio26/customerDataPlatform_mongoDb.git']],
                        extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'backend']]
                    ])
                }
            }
        }
        stage('test') {
            steps {
                bat '''
                cd frontend
                npm i
                npm test
                '''
            }
        }
        stage('build run on local') {
            steps {
                bat'''
                # echo "%credentials('MONGO_DB_URL')%"
                # set MONGODBATLAS_CLUSTER_CONNECTIONSTRING=mongodb+srv://timotiusgiovandi:89i09BcdjTWUaBsU@cluster0.mqtw9jg.mongodb.net
                # set VITE_BACKEND_URL=https://backend:8081
                docker-compose up -d
                '''
            }
        }
    }
}

