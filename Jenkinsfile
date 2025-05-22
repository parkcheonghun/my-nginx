pipeline {
  agent any

  environment {
    HELM_RELEASE_NAME = "my-nginx"
    HELM_NAMESPACE = "default"
    CHART_DIR = "."              // 현재 디렉토리에 Helm chart가 있다고 가정
    KUBECONFIG_CREDENTIAL_ID = "kubeconfig" // Jenkins에 등록한 kubeconfig ID
  }

  stages {
    stage('Checkout') {
      steps {
        git url: 'https://github.com/parkcheonghun/my-nginx.git', branch: 'main'
      }
    }

    stage('Set up Kubeconfig') {
      steps {
        withCredentials([file(credentialsId: "${KUBECONFIG_CREDENTIAL_ID}", variable: 'KUBECONFIG_FILE')]) {
          sh """
          #!/bin/bash
            export HOME=/var/lib/jenkins
            'mkdir -p $HOME/.kube'
            'cp $KUBECONFIG_FILE $HOME/.kube/config'
          """
        }
      }
    }

    stage('Helm Lint') {
      steps {
        sh "helm lint ${CHART_DIR}"
      }
    }

    stage('Helm Upgrade or Install') {
      steps {
        sh """
          helm upgrade --install ${HELM_RELEASE_NAME} ${CHART_DIR} \
            --namespace ${HELM_NAMESPACE} --create-namespace
        """
      }
    }
  }

}
