node {

    checkout scm

    env.DOCKER_API_VERSION="1.37"
    
    sh "git rev-parse --short HEAD > commit-id"

    tag = readFile('commit-id').replace("\n", "").replace("\r", "")
    appName = "k8s-cicd-nginx-test"
    registryHost = "docker.io/xianbinghui/"
    imageName = "${registryHost}${appName}:${tag}"
    env.BUILDIMG=imageName

    kubeconfig = "--kubeconfig=/etc/kubeconfig/kubeconfig"
    dockerconfig = "--config /etc/dockerconfig/docker-config.json"

    stage "Build"
    
        sh "docker ${dockerconfig} build -t ${imageName} -f applications/k8s-cicd-nginx-test/Dockerfile applications/k8s-cicd-nginx-test"

    stage "Push"

        sh "docker ${dockerconfig}  push ${imageName}"

    stage "Deploy"

        sh "sed 's#__IMAGE__#'$BUILDIMG'#' applications/k8s-cicd-nginx-test/k8s/deployment.yaml | kubectl ${kubeconfig} apply -f -"
        sh "kubectl ${kubeconfig} rollout status deployment/k8s-cicd-nginx-test"
}
