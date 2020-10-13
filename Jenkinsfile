pipeline {
    agent any;

    stages {
        stage("Zeroth stage") {
            steps {
                script {
                    if (currentBuild.previousBuild) {
                        try {
                            copyArtifacts(projectName: currentBuild.projectName,
                                          selector: specific("${currentBuild.previousBuild.number}"))
                            def previousFile = readFile(file: "usefulfile.txt")
                            echo("The current build is ${currentBuild.number}")
                            echo("The previous build artifact was: ${previousFile}")
                        } catch(err) {
                            // ignore error
                        }
                    }
                }
            }
        }

        stage("First stage") {
            steps {
                echo("Hello")
                writeFile(file: "usefulfile.txt", text: "This file ${env.BUILD_NUMBER} is useful, need to archive it.")
                archiveArtifacts(artifacts: 'usefulfile.txt')
            }
        }

        stage("Error") {
            steps {
                error("Failed")
            }
        }
    }
}
