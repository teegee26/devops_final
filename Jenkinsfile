pipeline{
  agent any
  stages{
    stage('checkout'){
      steps{
        script {
          // Checkout Frontend Repository
          checkout([
              $class: 'GitSCM',
              branches: [[name: 'main']],
              userRemoteConfigs: [[url: 'https://github.com/timgio26/customerDataPlatform_react.git']],
              extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'frontend-app']]
          ])

          // Checkout Backend Repository
          checkout([
              $class: 'GitSCM',
              branches: [[name: 'main']],
              userRemoteConfigs: [[url: 'https://github.com/timgio26/customerDataPlatform_mongoDb.git']],
              extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'backend-app']]
          ])
          
        }  
      }
    }
    stage('test'){
            steps {
                bat '''
                cd frontend-app
                npm ci 
                npm test
                '''
            }
        }
    }
    stage('build'){
      steps{
        
      bat'''
      docker-compose build
      '''
      }
    }
  
  }

