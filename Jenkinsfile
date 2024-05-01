pipeline { 

    agent any 

    stages { 

        stage('Maven Compile') { 

            steps { 

                sh 'mvn compile' 

            } 

        } 

        stage('Test JUnit') { 

            steps { 

                sh 'mvn test -Dtest=PisteServicesImplTest' 

            } 

        } 

       stage('Maven Build') { 

            steps { 

                sh 'mvn clean install -DskipTests' 

            } 

        } 

	stage('SonarQube tests') {
             steps {
                withSonarQubeEnv('sonar') {
                     sh "mvn sonar:sonar -Dsonar.host.url=http://sonarqube:9000"
                 }
            }
        }

        stage('Nexus Deployment') { 

            steps { 

                sh "mvn deploy -U -DaltDeploymentRepository=deploymentRepo::default::http://nexus:8081/repository/maven-releases/ -DskipTests=true"

            } 

        } 

        stage('Building Image') { 

            steps { 

                sh 'docker build -t anasdaoud15/station-ski .' 

            } 

        } 

        stage('Pushing Image') { 

            steps { 

                script{
				    withCredentials([string(credentialsId: 'dockerhub_cred', variable:'dockerhub_cred')]) {
		                sh 'echo ${dockerhub_cred} | docker login -u anasdaoud15 --password-stdin'
		                // sh 'docker push anasdaoud15/station-ski'
				    }
                }

            } 

        } 

        stage('Docker Compose') { 

            steps { 

                sh 'docker compose up -d' 

            } 

        } 

    } 

    post { 

        always { 

            emailext ( 

                to: 'anas.daoud@esprit.tn', 

                subject: "Pipeline ${currentBuild.fullDisplayName} completed", 

                body: "The pipeline ended with result: ${currentBuild.result}", 

                mimeType: 'text/plain' 

            ) 

        } 

    } 

}
