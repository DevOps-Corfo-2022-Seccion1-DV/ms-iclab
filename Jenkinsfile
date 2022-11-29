def pipelineType
def git_repo = 'github.com:DevOps-Corfo-2022-Seccion1-DV/ms-iclab.git'
// def git_repo = 'github.com/DevOps-Corfo-2022-Seccion1-DV/ms-iclab'

pipeline {
    agent any
    tools {
        maven 'maven'
    }
    environment{
        commitId = sh(returnStdout: true, script: 'git rev-parse HEAD')
        commitcheck = sh(returnStdout: true, script: '[ "$CHANGE_BRANCH" = "" ] || git rev-parse origin/$CHANGE_BRANCH')
        result = sh(returnStdout: true, script: 'git log --format=%B HEAD -n1')
        author = sh(returnStdout: true, script: 'git log --pretty=%an HEAD -n1')
        GIT_COMMIT_USERNAME = sh (script: 'git show -s --pretty=%an', returnStdout: true ).trim()
        commitmsg = sh(returnStdout: true, script: 'git log --oneline -n 1 HEAD')
        projectname = sh(script: 'git remote get-url origin | cut -d "/" -f5', returnStdout: true)
        commiteremail = sh(returnStdout: true, script: 'git log --pretty=%ae HEAD -n1')
        jenkinsurl = sh(script: 'echo "${BUILD_URL}"' , returnStdout: true).trim()

    }
    //[Grupo2][Pipeline IC/CD][Rama: develop][Stage: build][Resultado: Éxito/Success].
    //[Grupo2][Pipeline IC/CD][Rama: re-v1-0-0][Stage: test][Resultado: Error/Fail].
    stages {
        stage('Checkout') {
            steps {
                cleanWs()
                checkout scm
            }
            // checkout scm
        }
        stage("Env Variables") {
            steps {
                
                sh "printenv"
                script {
                    if(env.BRANCH_NAME == 'main'){
                        pipelineType = "Pipeline CD"
                    }else{
                        pipelineType = "Pipeline CI"
                    }
                }
            }
        }
        // stage("Build"){
        //     when { anyOf { branch 'feature-*'; branch 'main' } }
        //     steps {
        //         // sh './mvnw clean compile -e'
        //         slackSend color: "good", message: "Building.. branch: "+env.BRANCH_NAME
        //     }
        //     post{
        //         success{
        //             slackSend color: "good", message: "Grupo 3 - " + pipelineType + " - Rama : " + env.BRANCH_NAME + " - Stage : " + env.STAGE_NAME + " - Success"
        //         }
        //         failure {
        //             slackSend color: "danger", message: "Grupo 3 - " + pipelineType + " - Rama : " + env.BRANCH_NAME + " - Stage : " + env.STAGE_NAME + " - Fail"
        //         }
        //     }
        // }
        // stage('Test') {
        //     when { anyOf { branch 'feature-*'; branch 'main' } }
        //     steps {
        //         // sh './mvnw test -e'
        //         slackSend color: "good", message: "Testing.. branch: "+env.BRANCH_NAME
        //     }
        //     post{
        //         success{
        //             slackSend color: "good", message: "Grupo 3 - " + pipelineType + " - Rama : " + env.BRANCH_NAME + " - Stage : " + env.STAGE_NAME + " - Success"
        //         }
        //         failure {
        //             slackSend color: "danger", message: "Grupo 3 - " + pipelineType + " - Rama : " + env.BRANCH_NAME + " - Stage : " + env.STAGE_NAME + " - Fail"
        //         }
        //     }
        // }
        // stage('SonarQube') {
        //     when { anyOf { branch 'feature-*'; branch 'main' } }
        //     steps {
        //         // sh './mvnw sonar:sonar -e'
        //         slackSend color: "good", message: "SonarQube.. branch: "+env.BRANCH_NAME
        //         withSonarQubeEnv('sonar-public') { // If you have configured more than one global server connection, you can specify its name
        //             sh './mvnw clean package sonar:sonar'
        //         }
        //     }
        //     post{
        //         success{
        //             slackSend color: "good", message: "Grupo 3 - " + pipelineType + " - Rama : " + env.BRANCH_NAME + " - Stage : " + env.STAGE_NAME + " - Success"
        //         }
        //         failure {
        //             slackSend color: "danger", message: "Grupo 3 - " + pipelineType + " - Rama : " + env.BRANCH_NAME + " - Stage : " + env.STAGE_NAME + " - Fail"
        //         }
        //     }
        // }
        //generar pull request desde rama feature a main

        // stage('Package'){
        //     when { anyOf {  branch 'main' } }
        //     steps {
        //         // sh './mvnw package -e'
        //         slackSend color: "good", message: "Packaging.. branch: "+env.BRANCH_NAME
        //     }
        //     post{
        //         success{
        //             slackSend color: "good", message: "Grupo 3 - " + pipelineType + " - Rama : " + env.BRANCH_NAME + " - Stage : " + env.STAGE_NAME + " - Success"
        //         }
        //         failure {
        //             slackSend color: "danger", message: "Grupo 3 - " + pipelineType + " - Rama : " + env.BRANCH_NAME + " - Stage : " + env.STAGE_NAME + " - Fail"
        //         }
        //     }
        // }

        //hacer push en rama feature

        stage('lasttagnexus'){
            environment {
                GIT_AUTH = credentials('token-danilo')
            }
            when { anyOf { branch 'feature-*'; branch 'main' } }
            steps {
                script {
                    try {
                        sh 'git fetch --tags'
                        // lasttag = sh(returnStdout: true, script: 'git describe --tags `git rev-list --tags --max-count=1`')
                        // echo "lasttag: ${lasttag}"
                        lasttag = sh(returnStdout: true, script: 'git describe --abbrev=0 --tags')
                    }catch(Exception e){
                        lasttag = "v0.0.0"
                    }
                    echo "lasttag: "+lasttag
                    echo "mensaje commit : "+env.commitmsg
                    lasttag = lasttag.trim()
                    echo "lasttag: "+lasttag
                    lasttag = lasttag.substring(1)
                    echo "lasttag: "+lasttag
                    lasttag = lasttag.split("\\.")
                    echo "lasttag: "+lasttag
                    //ver si mensaje contiene una palabra
                    if(env.commitmsg.contains("major")){
                        lasttag = (lasttag[0].toInteger()+1)+".0.0"
                    }else if(env.commitmsg.contains("minor")){
                        lasttag = lasttag[0]+"."+(lasttag[1].toInteger()+1)+"."+lasttag[2]
                    }else if(env.commitmsg.contains("patch")){
                        lasttag = lasttag[0]+"."+lasttag[1]+"."+(lasttag[2].toInteger()+1)
                    }else{
                        echo "no contiene ninguna palabra"
                        currentBuild.result = 'ABORTED'
                        error("No se ha encontrado ninguna palabra clave para el incremento de version")
                    }
                    echo "lasttag: "+lasttag
                    //crear nuevo tag en repo
                    sh "git tag -a v"+lasttag+" -m 'v"+lasttag+"'"
                    sh "git config --global user.email 'danilovidalm@gmail.com'"
                    sh "git config --global user.name 'Jenkins'"
                    sh ('''
                        git config --local credential.helper "!f() { echo username=\\$GIT_AUTH_USR; echo password=\\$GIT_AUTH_PSW; }; f"
                        git push --tags
                    ''')

                    //enviar tag a nexus
                    // nexusUpload(
                    //     groupId: 'com.grupo3',
                    //     artifactId: 'grupo3',
                    //     version: lasttag,
                    //     packaging: 'jar',
                    //     file: 'target/grupo3-0.0.1-SNAPSHOT.jar',
                    //     repositoryId: 'nexus-public',
                    //     url: 'http://nexus:8081/repository/maven-public/'
                    // )
                
                    // sh "git push origin v"+lasttag
                    //actualizar version en pom.xml
                    // sh "mvn versions:set -DnewVersion="+lasttag+" -DgenerateBackupPoms=false"
                    // //subir cambios a repo
                    // sh "git add pom.xml"
                    // sh "git commit -m 'v"+lasttag+"'"
                    // sh "git push origin "+env.BRANCH_NAME
                    // //subir a nexus
                    // sh "mvn deploy -DskipTests"
                    // //slackSend color: "good", message: "lasttagnexus.. branch: "+env.BRANCH_NAME


                }
            }

        }


    }
}