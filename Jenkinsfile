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

        // stage('sonarqube'){
        //     steps{
        //         withSonarQubeEnv("SonarQube") {
        //                 sh "${tool("Sonar 4.8")}/bin/sonar-scanner \
        //                 -Dsonar.projectKey=java-maven-app \
        //                 -Dsonar.java.binaries=target \
        //                 -Dsonar.host.url=http://13.233.160.18:9000 \
        //                 -Dsonar.login=sqp_56c17308dacebf6e90d4fadb0afc60b35056a9d5"
        //             }
        //     }
        // }

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