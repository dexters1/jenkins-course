pipeline{
    agent any
    
    parameters{
        string(
            name: 'publish_name',
            defaultValue: 'results_default.zip',
            description: "descp"
            )
    }
    
    stages{
        stage("Download"){
            steps {
                cleanWs()
                echo "STEP 1"
                dir('pipeline'){
                    git(branch: 'pipeline', url:"https://github.com/rtrk-jenkins-course/jenkins-course.git")
                }
                rtDownload(
                    serverId: 'local-artifactory-2',
                    spec: '''{
                        "files": [
                        {
                            "pattern": "example-repo-local/printer.zip",
                            "target": "pipeline/"
                        }
                        ]
                    }
                    '''
                    )
                unzip(zipFile: "pipeline/printer.zip", 
                    dir: "pipeline"
                    )
            }
        }
        stage("Build"){
            steps {
                echo "STEP 2"
                dir('pipeline'){
                   bat (script: """Makefile.cmd
                    """)
                }
            }
            
        }
        stage("Publish"){
            steps {
                echo "STEP 3"
                zip(zipFile: "results.zip",
                    archive: true,
                    dir: "pipeline")
                rtUpload( serverId: 'local-artifactory-2',
                        spec: """{
                            "files": [
                            {
                                "pattern": "results.zip",
                                "target": "example-repo-local/${publish_name}"
                            }
                            ]
                            }""")
                script {bat 'exit 1'}
            }
        }
    }
    post {
        always{
            echo "ENDE"
        }
        failure{
            mail(to: "igorilic03@gmail.com",
                subject: "Jenkins brale",
                body: "hm")
        }
    }
}
