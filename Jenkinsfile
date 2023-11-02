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
                sh 'mvn sonar:sonar'
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

 
   
          
    
    }
}

