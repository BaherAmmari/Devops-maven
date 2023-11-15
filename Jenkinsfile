pipeline {
    agent any
    environment{
    DOCKER_LOGIN = 'baherammari'
    DOCKER_PASSWORD = 'Baher93886166'
    }
    stages {
        stage('Git') {
            steps {
               git branch: 'main', url: 'https://github.com/BaherAmmari/Devops-maven'
            }
        }
        stage('Cleaning the project') {
            steps {
               sh "mvn clean"
               echo 'clean the project'
            }
        }
        stage('Compiling the project') {
            steps {
               sh "mvn compile"
               echo 'compile the project'
            }
        }
         stage('Junit/Mockito Tests') {
            steps {
                sh "mvn test"
                echo 'tests unitaires'
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
                echo 'test statique'
              }
            }

        }

         stage('Artifact construction') {
            steps {
                sh "mvn package"
                echo 'construire artifact'
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
                    echo 'construire image docker'
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
                    echo 'push the image'
                }
                }
            }

         stage('Run Spring && MySQL Containers') {
                 steps {
                   sh 'docker-compose up -d'
                   echo 'executer les conteneurs'
                        }
                    }

}
post {
        success {
	        mail to: "baher.ammari@esprit.tn",
            subject: "Pipeline Backend Success ",
            body: "Welcome to DevOps project Backend : Success on job ${env.JOB_NAME}, Build Number: ${env.BUILD_NUMBER}, Build URL: ${env.BUILD_URL}"
        }
	    failure {
            mail to: "baher.ammari@esprit.tn",
            subject: "Pipeline backend Failure",
            body: "Welcome to DevOps project Backend : Failure on job ${env.JOB_NAME}, Build Number: ${env.BUILD_NUMBER}, Build URL: ${env.BUILD_URL} "
            }
    }
}
