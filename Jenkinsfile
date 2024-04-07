pipeline {
    agent any
    tools {
        maven 'MAVEN3'
        jdk 'OracleJDK8'
    }
    environment {
        SNAP_REPO = 'vprofile-snapshot'
        NEXUS_USER = 'admin'
        NEXUS_PASS = '19932'
        RELEASE_REPO = 'vprofile-release'
        CENTRAL_REPO = 'vpro-maven-central'
        NEXUSIP = '52.58.4.128'
        NEXUSPORT = '8081'
        NEXUS_GRP_REPO = 'vpro-maven-group'
        NEXUS_CREDENTIAL_ID = 'nexuslogin'
        SONARSERVER = "sonarserver"
        SONARSCANNER = "sonarscanner"
    } 
    stages {
        stage('Preparation') {
            steps {
                script {
                    sh """
                    envsubst < settings.xml.template > settings.xml
                    """
                }
            }
        }
        stage('Build') {
            steps {
                sh 'mvn -s settings.xml -U clean install -DskipTests'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('CODE ANALYSIS WITH CHECKSTYLE') {
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
            post {
                success {
                    echo 'Generated Analysis Result'
                }
            }
        }
        stage('CODE ANALYSIS with SONARQUBE') {
            environment {
                scannerHome = tool "${SONARSCANNER}"
            }
            steps {
                withSonarQubeEnv("${SONARSERVER}") {
                    sh """
                    ${scannerHome}/bin/sonar-scanner \
                    -Dsonar.projectKey=vprofile \
                    -Dsonar.projectName=vprofile \
                    -Dsonar.projectVersion=1.0 \
                    -Dsonar.sources=src/ \
                    -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                    -Dsonar.junit.reportsPath=target/surefire-reports/ \
                    -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                    -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml
                    """
                }
                timeout(time: 10, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}



        
