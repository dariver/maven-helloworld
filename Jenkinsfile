pipeline {
    agent any;
    options {
        copyArtifactPermission("$currentBuild.projectName");
    }
    stages {
        stage("Zeroth stage") {
            steps {
                script {
                    echo "previousCompletedBuild: " + currentBuild.previousCompletedBuild
                    echo "previousBuild: " + currentBuild.previousBuild
                    if (currentBuild.previousBuild) {
                        try {
                            echo "Copying files from previous build... " 
                            echo " ProjectName: " +  currentBuild.projectName
                            echo " selector: " +  specific("${currentBuild.previousBuild.number}")
                            sh "ls -la"
                            copyArtifacts(projectName: currentBuild.projectName,
                                          selector: lastSuccessful())
                            sh "ls -la"
                            def previousFile = readFile(file: "usefulfile.txt")
                            echo("The current build is ${currentBuild.number}")
                            echo("The previous build artifact was: ${previousFile}")
                        } catch(err) {
                            echo "" + err.getMessage()
                        }
                    }
                }
            }
        }

        stage("First stage") {
            steps {
                echo("Hello")
                writeFile(file: "usefulfile.txt", text: "This file ${env.BUILD_NUMBER} is useful, need to archive it.")
                sh "ls -la"
                archiveArtifacts(artifacts: 'usefulfile.txt')
            }
        }
    }
}
