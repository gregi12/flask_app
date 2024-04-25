pipeline {
    agent { label 'jenkins-agent' }
    stages {
    
        stage('Pre-build') {
                steps {
                    // Add pre-build steps here
                    sh '''
                          echo "Running pre-build steps"
                          docker system prune -a --volumes -f
                       '''
                    
                }
            }
        
        stage('Build') {
            steps {
                // Run Docker Compose instead of checking out Git branch
                sh '''
                    docker compose up -d --build
                    
                    docker exec -i flask_app-web-1 echo "1.13.3" > /app_version.txt
                        '''
                echo "$WORKSPACE"
        }
        }
        
        stage('Post-build') {
            environment{
                IMAGE_TAG = "latest"
                REPO_HOST = '10.61.15.7:5000'
                }
            
            steps {
                // Add post-build steps here
                 sh 'docker images'
                 script {
                     docker.withRegistry('https://10.61.15.7:5000', 'kredki_repo') {
                     sh '/home/jenkins/send_to_repo3.sh'
                     }
                }
        }
    
        

    post {
        always{
           sh 'docker compose down --remove-orphans -v'
           sh 'docker compose ps'
           sh 'docker system prune -a --volumes -f'
        }
        failure {
            mail to: 'staty1@o2.pl',
                 subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
                 body: "Something is wrong with ${env.BUILD_URL}"
        }
        success {
            echo 'I succeeded!'
        }
        
    }
        }    
}
}
