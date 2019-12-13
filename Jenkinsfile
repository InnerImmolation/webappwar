node(env.MASTER) {
    stage('Preparation') {
        git branch: 'master', url: 'https://github.com/InnerImmolation/webappwar.git'
        }
    stage('Build') {
        echo 'Build Time!'
        sh '/opt/apache-maven-3.6.2/bin/mvn clean package'
        }
    stage('Testing') {
        echo 'Testing Time!'
        parallel (
            'Unit Test': {sh '/opt/apache-maven-3.6.2/bin/mvn test'}
            )
       junit 'target/surefire-reports/*.xml'
       }
    stage ('Analysis') {
        echo 'Analysis Time!'
        withSonarQubeEnv(){
            sh '/opt/apache-maven-3.6.2/bin/mvn sonar:sonar -Dsonar.login=sonar -Dsonar.password=sonar -Dsonar.host.url=http://sonar'
            }
        }
    stage('Quality Gate') {
        echo 'Quality Gate Time!'
        timeout (time:5, unit:'MINUTES') {
            def qg = waitForQualityGate()
            if (qg.status != 'OK') {
                error "Pipeline aborted due to quality gate failure: ${qg.status}"
            }
        }
   }
   stage ('Publishing') {
      echo 'Publishing Time'
      nexusPublisher nexusInstanceId: 'nexus', nexusRepositoryId: 'maven-releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'target/mywebapp.war']], mavenCoordinate: [artifactId: 'mywebapp', groupId: 'com.dmitryd', packaging: 'war', version: '1.1']]]
      }
   stage('Archiving') {
        echo 'Archiving Time'
        archiveArtifacts 'target/mywebapp.war'
        }
  
}
