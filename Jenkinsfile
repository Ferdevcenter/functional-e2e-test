pipeline{

agent {
        kubernetes {
            // Rather than inline YAML, in a multibranch Pipeline you could use: yamlFile 'jenkins-pod.yaml'
            // Or, to avoid YAML:
            // containerTemplate {
            //     name 'shell'
            //     image 'ubuntu'
            //     command 'sleep'
            //     args 'infinity'
            // }
            yaml '''
apiVersion: v1
kind: Pod
spec:
   containers:
   - name: shell
     image: chikitor/jenkins-nodo-java-bootcamp:1.0
     volumeMounts:
      - mountPath: /var/run/docker.sock
        name: docker-socket-volume
     securityContext:
        privileged: true
   volumes:
   - name: docker-socket-volume
     hostPath:
       path: /var/run/docker.sock
       type: Socket
     command:
     - sleep
     args:
     - infinity
         '''
            // Can also wrap individual steps:
            // container('shell') {
            //     sh 'hostname'
            // }
            defaultContainer 'shell'
        }
    }
    stages {
      stage('Run function testing E2E') {
        steps {
         sh 'mvn clean verify -Dwebdriver.remote.url=http://standalone-chrome.default:4444 -Dwebdriver.remote.driver=chrome -Dchrome.switches="--no-sandbox,--ignore-certificate-errors,--homepage=about:blank,--no-first-run,--headless"'
        }
      }
      stage('Generate Cucumber Report') {
        steps {
          sh 'mvn serenity:aggregate'

          publishHTML(target: [
            reportName : 'Serenity',
            reportDir:   'target/site/serenity',
             reportFiles: 'index.html',
             keepAll:     true,
             alwaysLinkToLastBuild: true,
             allowMissing: false
          ])

        }
      }
    }

  post {
    always {
        sh 'docker logout'
    }
  }
}