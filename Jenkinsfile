pipeline{
    agent{
        label 'agent'
    }

    tools {
        maven 'Maven_3.9.6'
    }

    stages{
        stage('SCM Checkout'){
            steps{
                checkout changelog: false, poll: false, scm: scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Saravana011/java-maven-war-app.git']])
            }

        }

        stage('Maven-Build'){
            steps{
                sh 'mvn clean install'
            }
        }

        stage('Sonar Scan'){
            steps{
                withSonarQubeEnv("Sonarqube") {
                    sh "${tool("Sonar_5.0.1")}/bin/sonar-scanner \
                    -Dsonar.host.url=http://13.127.24.131:9000/ \
                    -Dsonar.login=sqp_1c6565f000b3e18a80fc4101eedf5535f6eed7b6 \
                    -Dsonar.java.binaries=target \
                    -Dsonar.projectKey=java-maven-war-app" 
                }
            }
        }

        stage('Nexus Upload'){
            steps{
                sh 'mvn -s settings.xml clean deploy'
            }
        }

        stage('deployment'){
            steps{
                sh 'ansible-playbook -i inventory deployment_playbook.yml -e "build_number=${BUILD_NUMBER}"'
            }
        }

    }
}
