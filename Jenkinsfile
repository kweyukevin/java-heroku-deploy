pipeline {
    agent { label 'linux' }
    
    environment {
        HEROKU_API_KEY = credentials('heroku_api_key')
        APP_NAME = "java-web-hub"
    }
    
    stages {
        stage('Build docker image') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/darinpope/java-web-app.git']]])
                sh 'sudo docker build -t kweyukevin/java-spring-app:latest .'
            }
        }
        stage('Ensure Heroku is installed'){
            steps{
                sh 'sudo curl https://cli-assets.heroku.com/install-ubuntu.sh | sh'
            }
        }
        stage('Login to Heroku using docker'){
            steps{
                sh 'echo $HEROKU_API_KEY | sudo  docker login --username=_ --password-stdin registry.heroku.com'
            }
        }
        stage('Push docker image to Heroku registry'){
            steps{
                sh '''
                    sudo docker tag kweyukevin/java-spring-app:latest registry.heroku.com/$APP_NAME/web
                    sudo docker push registry.heroku.com/$APP_NAME/web
                '''
            }
        }
        stage('Release image on Heroku'){
            steps{
                sh '''
                    sudo heroku container:release web --app=$APP_NAME
                '''
            }
        }
    }
    post {
    always {
      sh 'sudo docker logout'
    }
  }
}
