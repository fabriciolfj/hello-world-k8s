# jenkins spring kubernetes
- Instale o vagrant
- Comandos:
```
vagrant init -m (gera o arquivo)
vagrant up
```
- Dentro da maquina do jenkinsserver, instale o java, docker e o jenkins:
```
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -

sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
    
sudo apt-get update

sudo apt-get install jenkins

cat /var/log/jenkins/jenkins.log
```
- Dar permissão ao usuário do docker
```
sudo usermod -aG docker jenkins 
sudo service jenkins restart
``
- Exemplo pipeline
```
node {
    
    stage("Git Clone"){

        git credentialsId: 'GIT_HUB_CREDENTIALS', url: 'https://github.com/fabriciolfj/hello-world-k8s.git', branch: 'main'
    }
    
    stage('Gradle Build') {

       sh './gradlew build'

    }
    
    stage("Docker build"){
        sh 'docker version'
        sh 'docker build -t fabricio211/hello-demo .'
    }
    
    stage("Docker Login"){
        withCredentials([string(credentialsId: 'DOCKER_HUB_PASSWORD', variable: 'PASSWORD')]) {
            sh 'docker login -u fabricio211 -p $PASSWORD'
        }
    }
    
    stage("Push Image to Docker Hub"){
        sh 'docker push  fabricio211/hello-demo'
    }
    
        stage("SSH Into k8s Server") {
        def remote = [:]
        remote.name = 'K8S master'
        remote.host = '100.0.0.2'
        remote.user = 'vagrant'
        remote.password = 'vagrant'
        remote.allowAnyHosts = true

        stage('Put k8s-spring-boot-deployment.yml into k8smaster') {
            sshPut remote: remote, from: '/k8s/deployment.yml', into: '.'
        }

        stage('Deploy spring boot') {
          sshCommand remote: remote, command: "kubectl apply -f k8s-spring-boot-deployment.yml"
        }
    }
}
```
