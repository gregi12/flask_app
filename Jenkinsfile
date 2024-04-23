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
                sh 'docker compose up -d --build'
                echo "$WORKSPACE"
        }
        }
        
        stage('Post-build') {
            environment{
                IMAGE_TAG = "somethingElse"
                REPO_HOST = '10.61.15.7:5000'
                }
            
            steps {
                // Add post-build steps here
                 sh 'docker images'
                 script {
                     docker.withRegistry('http://10.61.15.7:5000', 'kredki_repo') {
                     sh '/home/jenkins/send_to_repo3.sh'
                     }
                }
        }
    
        

    post {
        always{
           sh 'docker compose down --remove-orphans -v'
           sh 'docker compose ps'
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
