stage 'Compile'
node('linux1') {
    checkout scm
    // use for non multibranch: git 'https://github.com/amuniz/maven-helloworld.git'
    try {
      def mvnHome = tool 'maven-3'
      sh "${mvnHome}/bin/mvn clean install -DskipTests"
      stash 'working-copy'
    } catch(e) {
      currentBuild.result = 'FAILURE'
      throw e
    } finally {
        echo "notify_fixed_or_failed..."
        notify_fixed_or_failed();
    }

}

stage 'Test'
parallel one: {
    node('linux1') {
        unstash 'working-copy'
        def mvnHome = tool 'maven-3'
        sh "${mvnHome}/bin/mvn test -Diterations=10"
    }
}, two: {
    node('linux1') {
        unstash 'working-copy'
        def mvnHome = tool 'maven-3'
        sh "${mvnHome}/bin/mvn test -Diterations=5"
    }
}, failFast: true


def notify_fixed_or_failed(String recipients=null) {
  if (is_fixed()) {
    echo "sending FIXED email"
    send_email('FIXED', recipients)
  } else if (get_status() == 'FAILURE') {
    echo "sending FAILURE email"
    send_email('FAILURE', recipients)
  }
}

def send_email(String status=null, String recipients=null) {

  status = status ?: get_status()
  def subject = "${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"

  def details = """
  <p>Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
  <p>Check console output at "<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>"</p>
  """

  currentBuild.result = null;
  if (recipients) {
    println "Email to recipients"
    emailext(subject: subject, body: details, to: recipients)
  } else {
    recipientsProvider = [
      [$class: 'CulpritsRecipientProvider'],
      [$class: 'DevelopersRecipientProvider'],
      [$class: 'RequesterRecipientProvider']
    ]
    println "Email to recipientsProvider"
    emailext(subject: subject, body: details, recipientProviders: recipientsProvider)
  }
}

def is_fixed() {
    def status = get_status()
    echo status
    return (status == 'SUCCESS' && !hudson.model.Result.SUCCESS.equals(currentBuild.rawBuild.getPreviousBuild()?.getResult()))
}
def get_status() {
    echo currentBuild.result
    return currentBuild.result ?: 'SUCCESS'
}
