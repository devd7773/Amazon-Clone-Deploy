pipeline{
    agent{
        label "amazon"
    }
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
    stages{
        stage('clean workspace'){
            steps{
                cleanWs()
            }
        }
        stage("pull the code from scm"){
            steps{
                git branch: 'main', url: 'https://github.com/devd7773/Amazon-Clone-Deploy.git'
            }
            
        }
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=amazon-deploy \
                    -Dsonar.projectKey=amazon-deploy '''
                }
            }
        }
        stage("Docker Build & Push"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'docker-hub-cred', variable: 'docker-hub')]) {
                        sh "docker build -t amazon-clone ."
                        sh "docker tag amazon-clone dev7773/amazon-clone:latest "
                        sh "docker login -u dev7773 -p ${docker-hub}"
                        sh "docker push dev7773/amazon-clone:latest"

                    }
                }
            }
        }
        stage('Deploy to container'){
            steps{
                sh 'docker run -dit --name amazon-clone -p 80:3000 dev7773/amazon-clone:latest'
            }
        }
    }
}
