pipeline {
  agent any

  options {
    timeout(time: 2, unit: 'MINUTES')
  }

  environment {
    ARTIFACT_ID = "elbuo8/webapp:${env.BUILD_NUMBER}"
  }
   stages {
           stage('Start Docker Registry') {
            steps {
                script {
                     // Verificar si el contenedor del registro de Docker ya está creado
            def registryExists = sh(script: "docker ps -a --filter 'name=registry' --format '{{.Names}}'", returnStdout: true).trim()

            if (registryExists == 'registry') {
                echo 'registry de Docker ya está creado. Continuando...'
            } else {
                // Iniciar el registro de Docker
                sh '''
                docker run -d -p 5000:5000 --restart=always --name registry registry:2
                '''
              echo 'Se inicio registry de docker'
                  }
                }
            }
        }
   stage('Building image') {
      steps{
          sh '''
        
          docker build -t testapp .
             '''  
        }
    }
  
  
    stage('Run tests') {
      steps {
        sh "docker run testapp npm test"
      }
    }
   stage('Deploy Image') {
      steps{
          script {
                    // Iniciar sesión en el registro
                    withCredentials([usernamePassword(credentialsId: 'docker-registry-creds', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) 
                    {
                        sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD 127.0.0.1:5000'
                    }
          }
        sh '''
        docker tag testapp 127.0.0.1:5000/mguazzardo/testapp
        docker push 127.0.0.1:5000/mguazzardo/testapp   
        '''
        }
      }
    }
}



    
  

