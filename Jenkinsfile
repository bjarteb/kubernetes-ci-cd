node {
    def app
    env.DOCKER_API_VERSION="1.23"

    checkout scm

    sh "git rev-parse --short HEAD > commit-id"

    tag = readFile('commit-id').replace("\n", "").replace("\r", "")
    appName = "hello-kenzan"
    registryHost = "bjarteb/"
    imageName = "${registryHost}${appName}:${tag}"
    env.BUILDIMG=imageName

    stage "Build"

        app = docker.build("${imageName}", "-f applications/hello-kenzan/Dockerfile applications/hello-kenzan" )
        // sh "docker build -t ${imageName} -f applications/hello-kenzan/Dockerfile applications/hello-kenzan"

    stage "Push"

        docker.withRegistry('', 'docker-hub-credentials') {
          app.push()    
        }

        // sh "docker login --username VAR_USERNAME --password VAR_PASSWORD"
        // sh "docker push ${imageName}"

    stage "Deploy"

        sh "sed 's#bjarteb/hello-kenzan:latest#'$BUILDIMG'#' applications/hello-kenzan/k8s/deployment.yaml | kubectl apply -f -"
        sh "kubectl rollout status deployment/hello-kenzan"
}
