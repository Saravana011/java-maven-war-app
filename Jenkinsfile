pipeline{
    agent{
        label 'agent'
    }

    tools {
        maven 'Maven.3.9.7'
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
                    sh "${tool("Sonar_4477")}/bin/sonar-scanner \
                    -Dsonar.host.url=http://13.127.8.79:9000/ \
                    -Dsonar.login=sqp_6da2375185e82f02c2282309920cf9da21f4a515 \
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
