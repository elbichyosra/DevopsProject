pipeline {
    agent any
 stages {
        stage('Build') {
            steps {
                // Étape pour compiler votre projet
                sh 'mvn clean'  // Exemple avec Apache Maven, adaptez à votre projet
            }
        }    
         stage('Compile') {
            steps {
                // Étape pour compiler votre projet
                sh 'mvn compile'  
            }
        } 
        
         stage ('Code Quality'){
            steps {
                    withSonarQubeEnv('SonarQubeServer') {
                  sh 'mvn sonar:sonar -Dsonar.login=squ_4c0376f480a00cb291609bff6737499b27b2d95e -Dsonar.host.url=http://192.168.33.10:9000/'

                }
             }
        }
        stage('JUNIT/MOCKITO') {
                    steps {
                        // Exécute les tests unitaires avec Maven et affiche les rapports
                        sh 'mvn test'
                    }
                    post {
                        always {
                            junit '**/target/surefire-reports/TEST-*.xml'
                        }
                    }
                }
          stage(' Artifact construction') {
            steps {
                // Étape pour construire l'artefact (par exemple, un fichier JAR)
                sh 'mvn package'
            }
            post {
                success {
                    archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true
                }
            }
        }
       stage('MVN_DEPLOY_TO_NEXUS') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'nexusConfig', usernameVariable: 'SONAR_USERNAME', passwordVariable: 'SONAR_PASSWORD')]) {
                        def mavenCmd = 'mvn deploy -DskipTests'
                        sh mavenCmd
                    }
                }
            }
        }
         stage('Download JAR from Nexus and Build Docker Image') {
            steps {
                script {
                    def jarUrl = 'http://localhost:8081/repository/maven-releases/com/example/DevOps_Project/0.0.1/DevOps_Project-2.1.jar'
                    sh "curl -o DevOps_Project-2.1.jar ${jarUrl}"

                    def dockerImage = 'imagebackend'
                    def dockerFile = 'Dockerfile'

                    // Construire l'image Docker
                    sh "docker build -t ${dockerImage} -f ${dockerFile} ."
                }
            }
        }

 
   
          
    
    }
}

