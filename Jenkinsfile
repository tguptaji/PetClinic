node {
    def mvnHome = tool name: 'MAVEN3', type: 'maven'
    def mvnCli = "${mvnHome}/bin/mvn"

    properties([
        buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '5')),
        disableConcurrentBuilds(),
        [$class: 'GithubProjectProperty', displayName: '', projectUrlStr: 'https://github.com/tguptaji/PetClinic.git/'],
        [$class: 'ThrottleJobProperty', categories: [], limitOneJobWithMatchingParams: false, maxConcurrentPerNode: 0, maxConcurrentTotal: 0, paramsToUseForLimit: '', throttleEnabled: true, throttleOption: 'project'],
        pipelineTriggers([githubPush()]),
        parameters([string(defaultValue: 'DEV', description: 'env name', name: 'environment', trim: false)])
    ])
    stage('Checkout SCM'){
        git branch: 'master', credentialsId: 'github-creds', url: 'https://github.com/tguptaji/PetClinic'
    }
    stage('Read praram'){
        echo "The environment chosen during the Job execution is ${params.environment}"
        echo "$JENKINS_URL"
    }
    stage('maven compile'){
        // def mvnHome = tool name: 'Maven_3.6', type: 'maven'
        // def mvnCli = "${mvnHome}/bin/mvn"
        sh "${mvnCli} clean compile"
    }
    stage('maven package'){
        sh "${mvnCli} package -Dmaven.test.skip=true"
    }
    stage('Archive atifacts'){
        archiveArtifacts artifacts: '**/*.war', onlyIfSuccessful: true
    }
    stage('Archive Test Results'){
        junit allowEmptyResults: true, testResults: '**/surefire-reports/*.xml'
    }
    stage('Deploy To Tomcat'){
        sshagent(['test']) {
            sh 'scp -o StrictHostKeyChecking=no target/*.war root@13.233.87.117:/opt/apache-tomcat-7.0.94/webapps/'
        }
    }
    stage('Smoke Test'){
        sleep 5
        sh "curl 13.233.87.117:8080/petclinic"
    }

}
