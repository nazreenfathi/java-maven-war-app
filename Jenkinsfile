pipeline{
    agent any

    tools {
        maven 'Maven'       
    }

    stages{
        stage('SCM Checkout'){
            steps{
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'git credentials', url: 'https://github.com/nazreenfathi/java-maven-war-app.git']])
            }
            
        }

        stage('maven-build'){
            steps{
                sh 'mvn clean install'
            }
        }

         stage('sonarqube'){
             steps{
                 withSonarQubeEnv("SonarQube"){
                         sh "${tool("sonarQube")}/bin/sonar-scanner \
                         -Dsonar.projectKey=java-maven-app \
                         -Dsonar.java.binaries=target \
                         -Dsonar.host.url=http://44.211.243.209:9000 \
                         -Dsonar.login=sqp_3e61e6c238a46cda602da959e97afada000883c7"
                    }
             }
         }

        stage('nexus-upload'){
            steps{
                sh 'mvn -s settings.xml clean deploy'
            }
        }

        stage("deployment"){
            agent{
                label 'Ansible'
            }
            steps{
                sh 'ansible-playbook -i inventory.yml deployment_playbook.yml -e "build_number=${BUILD_NUMBER}"'                
            }
        }
    }

}