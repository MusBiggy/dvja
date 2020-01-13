pipeline {
  agent any

  tools {
    maven "apache-maven-3.6.3"
  }

  stages {
    stage('Build') {
      steps {
        git 'https://github.com/ajlanghorn/dvja.git'
        sh "mvn clean package"
      }
    }
    stage('Check dependencies') {
      steps {
        dependencyCheck additionalArguments: '', odcInstallation: 'Dependency-Check'
        dependencyCheckPublisher failedTotalCritical: 11, failedTotalHigh: 41, failedTotalLow: 10, failedTotalMedium: 35, pattern: '', unstableTotalCritical: 1, unstableTotalHigh: 1, unstableTotalLow: 10, unstableTotalMedium: 5

        
      }
    }
    stage('Analysis') {
      steps {
        sh "mvn --batch-mode -V -U -e checkstyle:checkstyle pmd:pmd pmd:cpd spotbugs:spotbugs"
      }
    }


    stage('Publish to S3') {
      steps {
        sh "aws s3 cp /var/lib/jenkins/workspace/dvja/target/dvja-1.0-SNAPSHOT.war s3://ako202020-buildartifacts-sx8r7qb5jj4/dvja-1.0-SNAPSHOT.war"
      }
    }
    stage('Tidy up') {
      steps {
        cleanWs()
      }
    }
    post {
      always {
        recordIssues enabledForFailure: true, tools: [mavenConsole(), java(), javaDoc()]
        recordIssues enabledForFailure: true, tool: checkStyle()
        recordIssues enabledForFailure: true, tool: spotBugs()
        recordIssues enabledForFailure: true, tool: cpd(pattern: '**/target/cpd.xml')
        recordIssues enabledForFailure: true, tool: pmdParser(pattern: '**/target/pmd.xml')
      }
    }

  }
}
