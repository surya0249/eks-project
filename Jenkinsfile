node {

    def mvnHome = tool 'Maven3'

    stage ('Checkout') {
        checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'e2f1b466-b418-42ac-84a8-b6d6a5857928', url: 'https://github.com/surya0249/eks-project.git']]])

    }

    stage ('build')  {
        sh "${mvnHome}/bin/mvn package -f MyAwesomeApp/pom.xml"
    }

    stage ('Docker Build') {
     // Build and push image with Jenkins' docker-plugin
    withDockerServer([uri: "tcp://localhost:4243"]) {
        withDockerRegistry([credentialsId: "dockerhub", url: "https://index.docker.io/v1/"]) {
        image = docker.build("suryasajja/myapp", "eks-project")
        image.push()
        }
      }
    }

    stage ('Kubernetes Deploy') {
        kubernetesDeploy(
            configs: 'eks-project/myweb.yml',
            kubeconfigId: 'K8S',
            enableConfigSubstitution: true
            )
    }
    /*
        stage ('Kubernetes Deploy using Kubectl') {
          sh "kubectl apply -f MyAwesomeApp/springBootDeploy.yml"
    }*/
}

