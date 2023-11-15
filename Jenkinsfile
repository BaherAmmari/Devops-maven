pipeline {
    agent any
    environment{
    DOCKER_LOGIN = 'baherammari'
    DOCKER_PASSWORD = 'Baher93886166'
    }
    stages {
        stage('Git') {
            steps {
               git branch: 'main', url: 'https://github.com/devopsSpring/Devops'
            }
        }
        stage('Cleaning the project') {
            steps {
               sh "mvn clean"
            }
        }
        stage('Compiling the project') {
            steps {
               sh "mvn compile"
            }
        }
         stage('Junit/Mockito Tests') {
            steps {
                sh "mvn test"
            }

             post {
                always {
                    junit allowEmptyResults: true, testResults: 'target/surefire-reports/*.xml'
                }
            }
        }
        stage("SonarQube analysis") {
            steps {
              withSonarQubeEnv('sonarQubeServer') {
                sh 'mvn sonar:sonar'
              }
            }

        }

         stage('Artifact construction') {
            steps {
                sh "mvn package"
            }

        }
         stage('Nexus') {
            steps {
                nexusArtifactUploader artifacts: [
                    [
                        artifactId: 'DevOps_Project',
                        classifier: '',
                        file: 'target/DevOps_Project-2.1.jar',
                        type: 'jar'
                        ]
                        ],
                credentialsId: 'nexus3',
                groupId: 'tn.esprit',
                nexusUrl: '192.168.33.10:8081',
                nexusVersion: 'nexus3',
                protocol: 'http',
                repository: 'maven-releases',
                version: '2.1'
            }

        }
         stage('Docker build') {
            steps {
                script{
                    sh "docker build -t baherammari/app-spring-repo ."
                }
                }
            }
        stage('Docker login') {
                steps {
                    sh 'docker login -u $DOCKER_LOGIN -p $DOCKER_PASSWORD'
                    echo 'Docker login'

                     }
                    }
        stage('Pushing Docker Image') {
            steps {
                script{
                    sh 'docker push baherammari/app-spring-repo'
                }
                }
            }

         stage('Run Spring && MySQL Containers') {
                 steps {
                   sh 'docker-compose up -d'
                        }
                    }

}
}
