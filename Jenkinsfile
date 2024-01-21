pipeline {
    agent {
        label "sul-ansible"
    }

    options {
      disableConcurrentBuilds()
      ansiColor("xterm")
    }

    parameters {
        string(defaultValue: "0.0.0.0", name: "Server")
    }

    stages {

        stage('Ansible Clone Repo') {
            steps {
                container('ansible') {
                    git credentialsId: 'bitbucket_token', url: 'https://github.com/fchelotti/ansibleagentes_linux.git', branch: 'develop'
                }
            }
        }

        stage('Pre Build') {
            steps {
                container('ansible') {
                    sh 'for ip in ${Server}; do sed -i "/linux/a $ip" Modulos/hosts; done'
                }
            }
        }

        stage('Apply Agents') {
            steps {
                withCredentials([string(credentialsId: 'jenkins_admin_passwd', variable: 'ansible_passwd')]){
                container('ansible') {
                    script {
                        sh 'ansible-playbook -i Modulos/hosts Modulos/agentes-linux-sul.yaml -e "ansible_password=${ansible_passwd}" -e "cluster="'
                    }
                }
            } 
        }
    }
}
}