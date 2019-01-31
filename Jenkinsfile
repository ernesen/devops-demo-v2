<<<<<<< HEAD
podTemplate(
    label: 'mypod', 
    inheritFrom: 'default',
    containers: [
        containerTemplate(
            name: 'golang', 
            image: 'golang:1.10-alpine',
            ttyEnabled: true,
            command: 'cat'
        ),
        containerTemplate(
            name: 'docker', 
            image: 'docker:18.02',
            ttyEnabled: true,
            command: 'cat'
        ),
        containerTemplate(
            name: 'helm', 
            image: 'ibmcom/k8s-helm:v2.6.0',
            ttyEnabled: true,
            command: 'cat'
        )
    ],
    volumes: [
        hostPathVolume(
            hostPath: '/var/run/docker.sock',
            mountPath: '/var/run/docker.sock'
        )
    ]
) {
    node('mypod') {
        def commitId
        stage ('Extract') {
            checkout scm
            commitId = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
        }
        stage ('Build') {
            container ('golang') {
                sh 'CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o main .'
            }
        }
        def repository
        stage ('Docker') {
            container ('docker') {
                def registryIp = sh(script: 'getent hosts registry.kube-system | awk \'{ print $1 ; exit }\'', returnStdout: true).trim()
                repository = "${registryIp}:80/hello"
                sh "docker build -t ${repository}:${commitId} ."
                sh "docker push ${repository}:${commitId}"
            }
        }
        stage ('Deploy') {
            container ('helm') {
                sh "/helm init --client-only --skip-refresh"
                sh "/helm upgrade --install --wait --set image.repository=${repository},image.tag=${commitId} hello hello --tls"
            }
        }
    }
}
=======
//def label = "worker-${UUID.randomUUID().toString()}"

podTemplate(label: 'jenkins-pipeline', containers: [
  //containerTemplate(name: 'gradle', image: 'gradle:4.5.1-jdk9', command: 'cat', ttyEnabled: true),
  containerTemplate(name: 'docker', image: 'docker', command: 'cat', ttyEnabled: true) //,
  //containerTemplate(name: 'kubectl', image: 'lachlanevenson/k8s-kubectl:v1.8.8', command: 'cat', ttyEnabled: true),
  //containerTemplate(name: 'helm', image: 'lachlanevenson/k8s-helm:latest', command: 'cat', ttyEnabled: true)
],
volumes: [
  hostPathVolume(mountPath: '/home/gradle/.gradle', hostPath: '/tmp/jenkins/.gradle'),
  hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock')
]) {
node('masternode') {

    def myRepo = checkout scm
    def gitCommit = myRepo.GIT_COMMIT
    def gitBranch = myRepo.GIT_BRANCH
    def shortGitCommit = "${gitCommit[0..10]}"
    def previousGitCommit = sh(script: "git rev-parse ${gitCommit}~", returnStdout: true)
 /*
    stage('Test') {
      try {
        container('gradle') {
          sh """
            pwd
            echo "GIT_BRANCH=${gitBranch}" >> /etc/environment
            echo "GIT_COMMIT=${gitCommit}" >> /etc/environment
            gradle test
            """
        }
      }
      catch (exc) {
        println "Failed to test - ${currentBuild.fullDisplayName}"
        throw(exc)
      }
    }
    stage('Build') {
      container('gradle') {
        sh "gradle build"
      }
    }
*/
    stage('Create Docker images') {
      container('docker') {
        withCredentials([[$class: 'UsernamePasswordMultiBinding',
          credentialsId: 'dockerhub',
          usernameVariable: 'DOCKER_HUB_USER',
          passwordVariable: 'DOCKER_HUB_PASSWORD']]) {
          sh """
            docker login -u ${DOCKER_HUB_USER} -p ${DOCKER_HUB_PASSWORD}
            docker build -t ernesen/my-image:${gitCommit} .
            docker push ernesen/my-image:${gitCommit}
            """
        }
      }
    }
  
/*
    stage('Run kubectl') {
      container('kubectl') {
        sh "kubectl get pods"
      }
    }
    stage('Run helm') {
      container('helm') {
        sh "helm list"
      }
    }
*/
  }
}
>>>>>>> b5bcf80259c091dba941b592eab09542f37d9ef9
