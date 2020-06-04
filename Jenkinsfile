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


}

