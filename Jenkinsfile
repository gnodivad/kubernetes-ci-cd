node {

    checkout scm

    env.DOCKER_API_VERSION="1.23"
    
    sh "git rev-parse --short HEAD > commit-id"

    tag = readFile('commit-id').replace("\n", "").replace("\r", "")
    appName = "hello-kenzan"
    registryHost = "davidodw/"
    imageName = "${registryHost}${appName}:${tag}"
    env.BUILDIMG=imageName

    stage "Build"

        def customImage = docker.build("${imageName}", "-f applications/hello-kenzan/Dockerfile applications/hello-kenzan")
    
    stage "Push"

        docker.withRegistry('https://registry.hub.docker.com', 'docker-hub') {
            customImage.push()
            customImage.push('latest')
        }
        
    stage "Deploy"

        sh "sed 's#127.0.0.1:30400/hello-kenzan:latest#'$BUILDIMG'#' applications/hello-kenzan/k8s/deployment.yaml | kubectl apply -f -"
        sh "kubectl rollout status deployment/hello-kenzan"
}