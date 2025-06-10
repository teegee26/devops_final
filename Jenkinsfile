pipeline {
    agent any
    environment{
        APP_VERSION = "1.0.${BUILD_ID}"
    }
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
                docker-compose up -d
                '''
                sleep 30
            }
        }

        stage('SonarQube analysis') {
              steps {
                script {
                    scannerHome = tool 'sonarcubescanner'// must match the name of an actual scanner installation directory on your Jenkins build agent
                }
                withSonarQubeEnv('mysonarcube') {// If you have configured more than one global server connection, you can specify its name as configured in Jenkins
                  bat "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=devops_final -Dsonar.sources=frontend/src"                  
                }
              }
        }

        stage('QA approval'){
            steps {
                echo 'open http://localhost:3000/'
                input message: 'Do you want to continue to deployment?', ok: 'Yes, deploy'
            }
        }

        stage('Deploy with Ansible in WSL') {
            steps {
                script {
                    def ansibleInstalled = bat(script: "wsl -d Ubuntu ansible-playbook -i inventory.ini playbook.yml", returnStatus: true) == 0
                    if (!ansibleInstalled) {
                        echo "Ansible is not installed inside WSL. Skipping deployment."
                    } else if (!fileExists('inventory.ini') || !fileExists('playbook.yml')) {
                        echo "Required Ansible files (inventory.ini or playbook.yml) missing. Skipping deployment."
                    } else {
                        bat "wsl ansible-playbook -i inventory.ini playbook.yml"
                    }
                }
            }
        }

        stage('deployment to production'){
            steps {
                echo('deploy to production')
                bat'''
                az account show 
                '''
            }
        }
    }
    post{
        success{
            bat'''
            echo last success build : %APP_VERSION% > lastsuccessbuild.txt
            '''
            mail bcc: '', body: "pipeline running successfuly ${APP_VERSION }", cc: '', from: '', replyTo: '', subject: 'jenkins pipeline result', to: 't.giovandi.523@studms.ug.edu.pl'
        }
        failure{
            bat'''
            echo fail pipeline please refer to:
            more lastsuccessbuild.txt
            '''
        }
    }
}

