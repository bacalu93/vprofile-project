def COLOR_MAP = [
    "SUCCESS": "good", // Corrected typo 'SUCCES' to 'SUCCESS'
    "FAILURE": "danger",   
]

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
        NEXUSIP = '172.31.28.189'
        NEXUSPORT = '8081'
        NEXUS_GRP_REPO = 'vpro-maven-group'
        NEXUS_CREDENTIAL_ID = 'nexuslogin'
        SONARSERVER = "sonarserver"
        SONARSCANNER = "sonarscanner"
        NEXUS_LOGIN = "nexuslogin"
    } 
    stages {
        stage('Preparation') {
            steps {
                script {
                    sh "envsubst < settings.xml.template > settings.xml"
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
            }
        }
        stage('Quality Gate') {
            steps {
                timeout(time: 15, unit: 'MINUTES') {
                    script {
                        def qg = waitForQualityGate() // This will return a QualityGate object
                        echo "Quality Gate Status: ${qg.status}"
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure"
                        }
                    }
                }
            }
        }
        stage("UploadArtifact") { // Duplicated stage name corrected
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
                    groupId: 'QA',
                    version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                    repository: "${RELEASE_REPO}",
                    credentialsId: "${NEXUS_LOGIN}",
                    artifacts: [
                        [artifactId: "vproapp",
                         classifier: '',
                         file: 'target/vprofile-v2.war',
                         type: 'war']
                    ]
                )
            }
        }
    }
    // post {
    //     always {
    //         echo 'Slack Notification'
    //         slackSend(
    //             channel: '#jenkinscicd',
    //             color: COLOR_MAP[currentBuild.currentResult],
    //             message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL} "
    //         )
    //     }
    // }
}



        
