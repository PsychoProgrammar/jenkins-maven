pipeline {

    agent {

        label "master"

    }

    tools {

        maven "MAVEN"

    }

    environment {

        NEXUS_VERSION = "nexus3"

        NEXUS_PROTOCOL = "http"

        NEXUS_URL = "http://18.222.198.245:8081/"

        NEXUS_REPOSITORY = "maven-nexus-repo"

        NEXUS_CREDENTIAL_ID = "NEXUS_CRED"

    }
    stages {
        stage('Build Maven') {
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'GIT_REPO', url: 'https://github.com/devopshint/jenkins-maven.git']]])

                sh "mvn -Dmaven.test.failure.ignore=true clean package"
                
            }
        }
    stage("Publish to Nexus Repository Manager") {

            steps {

                script {

                    pom = readMavenPom file: "pom.xml";

                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");

                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"

                    artifactPath = filesByGlob[0].path;

                    artifactExists = fileExists artifactPath;

                    if(artifactExists) {

                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";

                        nexusArtifactUploader(

                            nexusVersion: nexus3,

                            protocol: http,

                            nexusUrl: "http://18.222.198.245:8081/"

                            groupId: com.mycompany.app,

                            version: 1.0-SNAPSHOT,

                            repository: maven-nexus-repo,

                            credentialsId: NEXUS_CRED,

                            artifacts: [

                                [artifactId: pom.artifactId,

                                classifier: '',

                                file: artifactPath,

                                type: pom.packaging],

                                [artifactId: pom.artifactId,

                                classifier: '',

                                file: "pom.xml",

                                type: "pom"]

                            ]

                        );

                    } else {

                        error "*** File: ${artifactPath}, could not be found";

                    }

                }

            }

        }

    }

}