node {

    env.PATH=env.PATH+":/opt/gradle-6.4.1/bin:/opt/groovy-3/bin"
    def student = "mzubov"
    def child_job = "MNTLAB-${student}-child1-build-job"
    def child_artifact = "${student}_dsl_script.tar.gz"

    stage 'Preparation (Checking out)'
        git url: "/home/student/mntlab-pipeline", branch: 'mzubov'

 
    stage 'Building code'
        sh 'gradle build'


    stage 'Testing code'
        parallel (
                'Unit Tests': {sh 'gradle test' },
                'Jacoco Tests': {sh 'gradle jacocoTestReport' },
                'Cucumber Tests': {sh 'gradle cucumber' }
                )


    stage ' Triggering job and fetching artifacts after finishing'
        build job: "${child_job}", \
            parameters: [string(name: 'BRANCH_NAME', value: "${student}")], wait: true
        step([$class: 'CopyArtifact',
            projectName: "${child_job}",
            filter: "${child_artifact}"]);


    stage 'Packing and Publishing results'
        sh "tar -xzf ${child_artifact}"
        sh "cp build/libs/${JOB_NAME}.jar gradle-simple.jar"
        sh "tar -zcf ${student}-${BUILD_NUMBER}.tar.gz jobs.groovy Jenkinsfile gradle-simple.jar"
        archiveArtifacts "${student}-${BUILD_NUMBER}.tar.gz"


    stage 'Asking for manual approval' 
        timeout(time:1, unit:'HOURS') {
        input message:'Please approve current deployment', ok: 'Yes'
        }


    stage 'Deployment' 
        sh 'java -jar gradle-simple.jar '


}

