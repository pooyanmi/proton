def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
]
pipeline {
    agent any
    tools {
        maven "MAVEN3.9"
        jdk "JDK17"
       
    }
    
    environment {
        SNAP_REPO = 'maven-snapshots'
		NEXUS_USER = 'admin'
		NEXUS_PASS = 'Pp@123456!'
		RELEASE_REPO = 'Maven-Pooyan-Releases'
		CENTRAL_REPO = 'maven-central'
		NEXUSIP = '192.168.56.15'
		NEXUSPORT = '8081'
		NEXUS_GRP_REPO = 'Maven-Pooyan-Group'
        NEXUS_LOGIN = 'nexuslogin'

    }

    

    stages {

        stage('Checkout SCM') {
            steps {
                git branch: 'jenkins-ci',
                    url: 'git@github.com:pooyanmi/proton.git',
                    credentialsId: 'gitlogin'
            }
        }

        stage('Build'){
            steps {
                   withEnv([
                     "MAVEN_OPTS=-Xms512m -Xmx2048m"
                  ]) {
                     sh 'mvn -s settings.xml clean install -DskipTests -U'
                  }
            }
            post {
                success {
                    echo "Now Archiving."
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }

        stage('Test'){
            steps {
                sh 'mvn -s settings.xml test'
            }

        }

        

       

        stage("UploadArtifact"){
            steps{
                nexusArtifactUploader(
                  nexusVersion: 'nexus3',
                  protocol: 'http',
                  nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
                  groupId: 'QA',
                  version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                  repository: "${RELEASE_REPO}",
                  credentialsId: "${NEXUS_LOGIN}",
                  artifacts: [
                    [artifactId: 'vproapp',
                     classifier: '',
                     file: 'target/vprofile-v2.war',
                     type: 'war']
                  ]
                )
            }
        }

    }

}