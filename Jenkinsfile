pipeline {
    agent {label "unix"}
    parameters {
	choice(choices: 'build\ndev\nqa',name:'Stage')  
	}
	stages {
        stage('scm-checkout') {
                steps {
                        checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/SandeepOnta/maven-project.git']]])
                  }
        }
        stage('build'){
				steps {
					script {
						if (Stage in ["build", "dev"]){		                
							sh 'mvn clean install'
						}
					}
				}
			}
        stage('archive'){
                steps {
                        archiveArtifacts '**/target/*.war'
            }
        }
        stage('upload to artifactory'){
                steps {
                        sh 'curl -X PUT -u admin:AP7quuBtD3b5k9jtWRBTwssjyvw -T ${WORKSPACE}/webapp/target/webapp.war "http://192.168.0.31:8081/artifactory/libs-release-local/com/mkyong/CounterWebApp/webapp-1.0.war"'
            }
        }
		stage('deploy to tomcat from ansible playbook'){
                steps {
						ansiblePlaybook credentialsId: '99233ea5-4303-4b34-b07f-d4c10b55ce6b', inventory: '/etc/ansible/hosts', playbook: 'deploy-tomcat-ansible.yml', sudoUser: null

				}
        }
    }
}
