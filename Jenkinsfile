pipeline {
  agent {
    kubernetes {
      label 'kaniko'
      yaml """
kind: Pod
metadata:
  name: kaniko
spec:
  containers:
  - name: jnlp
    workingDir: /tmp/jenkins
  - name: kaniko
    workingDir: /tmp/jenkins
    image: gcr.io/kaniko-project/executor:debug
    imagePullPolicy: Always
    command:
    - /busybox/cat
    tty: true
    volumeMounts:
      - name: jenkins-docker-cfg
        mountPath: /kaniko/.docker
  volumes:
  - name: jenkins-docker-cfg
    projected:
      sources:
      - secret:
          name: docker-hub
          items:
            - key: .dockerconfigjson
              path: config.json
"""
    }
  }
  stages {
    stage('Building image') {
      environment {
        PATH = "/busybox:/kaniko:$PATH"
      }
      steps {
        container(name: 'kaniko', shell: '/busybox/sh') { 
          sh '''#!/busybox/sh
            /kaniko/executor --context `pwd` --verbosity debug --destination arknou/APP:03
          '''
        }
      }
    }
  }
}
