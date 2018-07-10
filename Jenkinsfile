node {

    checkout scm

    env.DOCKER_API_VERSION="1.37"
    
    sh "git rev-parse --short HEAD > commit-id"

    tag = readFile('commit-id').replace("\n", "").replace("\r", "")
    appName = "k8s-cicd-nginx-test"
    registryHost = "docker.io/xianbinghui/"
    imageName = "${registryHost}${appName}:${tag}"
    env.BUILDIMG=imageName

    stage "Build"
    
        sh "docker build -t ${imageName} -f applications/k8s-cicd-nginx-test/Dockerfile applications/k8s-cicd-nginx-test"

    stage "Push"

        sh "docker push ${imageName}"

    stage "Deploy"

        sh "sed 's#__IMAGE__#'$BUILDIMG'#' applications/k8s-cicd-nginx-test/k8s/deployment.yaml | kubectl apply -f -"
        sh "kubectl rollout status deployment/k8s-cicd-nginx-test"
}
