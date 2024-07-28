  def label = "eosagent"
  def env = "dev"
  podTemplate(label: label, yaml: """
  apiVersion: v1
  kind: Pod
  metadata:
    labels:
      name: build
  spec:
    serviceAccount: jenkins-admin
    containers:
    - name: build
      image: qwerty703/eos-jenkins-agent-base:latest
      command:
      - cat
      tty: true
  """
  ) {
      node (label) {

          stage ('Checkout SCM'){
            git credentialsId: 'git', url: 'https://github.com/prpd1/eureka-registry-deploy.git', branch:  "${env}"
          }

          stage ('Helm Chart') {
            container('build') {
                withCredentials([usernamePassword(credentialsId: 'jfrog', usernameVariable: 'username', passwordVariable: 'password')]) {
                      sh '/usr/local/bin/helm repo add eos-helm-helm-local  https://eosadmin.jfrog.io/artifactory/eos-helm-helm-local --username $username --password $password'
                      sh "/usr/local/bin/helm repo update"
                      sh "/usr/local/bin/helm upgrade  --install --force registry-api  --namespace ${env} -f values.yaml eos-helm-helm-local/registry-api"
                      sh "/usr/local/bin/helm list -a --namespace ${env}"
                      sh "rm -rf values.yaml"
              }
          }
          }
      }
  }
