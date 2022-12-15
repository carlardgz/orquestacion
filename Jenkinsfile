pipeline {

  environment {
    dockerimagename1 = "carlarodriguezag/proyecto:cra"
    dockerimagename2 = "carlarodriguezag/phpmyadmin:cra"
    dockerImage1 = ""
    dockerImage2= ""
  }

  agent any

  stages {

    stage('Checkout Source') { 
      steps {
        git credentialsId: 'demo_github', url: 'https://github.com/carlardgz/orquestacion.git', branch:'main'
      }
    }

    stage('Build image') {
      steps{
	dir('proyecto') {
         script {        
	   dockerImage1 = docker.build dockerimagename1
          }
        }
	
	dir('phpmyadmin') {
	 script {
           dockerImage2 = docker.build dockerimagename2
          }
        }

      }
   }

    stage('Pushing Image') {
      environment {
               registryCredential = 'demo_dockerhub'
           }
      steps{
	dir('proyecto') {
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("cra")
          }
        }
      }

        dir('phpmyadmin') {
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("cra")
          }
        }
      }
    }
  }

   //stage('Deploying App to Kubernetes') {
   //  steps {
   //    script {
   //      //kubernetesDeploy(configs: "deployment-service-simplesaml.yaml", kubeconfigId: "kuberhaep")
   //       //sh 'microk8s.kubectl rollout restart prueba-gha'
   //     }        
   //   }
   // }

   stage('Restarting POD'){
   steps{
    sshagent(['rodriguezssh'])
    {
     sh 'cd proyecto && scp -r -o StrictHostKeyChecking=no deployment.yaml digesetuser@148.213.1.131:/home/digesetuser/'
      script{
        try{
           sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl apply -f deployment.yaml --kubeconfig=/home/digesetuser/.kube/config'
           sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl rollout restart deployment proyecto --kubeconfig=/home/digesetuser/.kube/config' 
           sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl rollout status deployment proyecto --kubeconfig=/home/digesetuser/.kube/config'
          }catch(error)
       {}
     
    sh 'cd phpmyadmin && scp -r -o StrictHostKeyChecking=no deployment.yaml digesetuser@148.213.1.131:/home/digesetuser/'
      script{
        try{
           sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl apply -f deployment.yaml --kubeconfig=/home/digesetuser/.kube/config'
           sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl rollout restart deployment phpmyadmin-deployment --kubeconfig=/home/digesetuser/.kube/config'
           sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl rollout status deployment phpmyadmin-deployment --kubeconfig=/home/digesetuser/.kube/config'
          }catch(error)
       {}
     }
    }
  }
 }
}
}
}
