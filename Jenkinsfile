throttle(['throttleDocker']) {
  node('docker') {
    wrap([$class: 'AnsiColorBuildWrapper']) {
      try{
        stage('Setup') {
          checkout scm
          sh '''
            ./ci/docker-down.sh
            ./ci/docker-up.sh
          '''
        }
        stage('Test'){
          parallel (
            "unit": {
              sh '''
                ./ci/test/unit.sh
              '''
            },
            "functional": {
              sh '''
                #./ci/test/functional.sh
              '''
            }
          )
        }
        stage('Capacity Test') {
          sh '''
            #./ci/test/stress.sh
          '''
        }
        stage('Deploy to Kubernetes') {
          sh '''
            version=$(date +%Y%m%d%H%M)
            ./cd/publish.sh prod registry.yaas-k8s-cluster.com:5000 $version
            ./cd/deploy-kube.sh prod registry.yaas-k8s-cluster.com:5000 $version
          '''
        }
      }
      finally {
        stage('Cleanup') {
          sh '''
            ./ci/docker-down.sh
          '''
        }
      }
    }
  }
}
