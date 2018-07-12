node {

    checkout scm

    env.DOCKER_API_VERSION="1.37"
    
    sh "git rev-parse --short HEAD > commit-id"

    tag = readFile('commit-id').replace("\n", "").replace("\r", "")
    appName = "k8s-cicd-nginx-test"
    registryHost = "docker.io/xianbinghui/"
    imageName = "${registryHost}${appName}:${tag}"
    env.BUILDIMG=imageName

    kubeconfig = "/etc/kubeconfig/config"
    dockerconfig = "/etc/dockerconfig"

    stage "Build"
    
        sh "docker --config ${dockerconfig} build -t ${imageName} -f applications/k8s-cicd-nginx-test/Dockerfile applications/k8s-cicd-nginx-test"

    stage "Push"

        sh "docker --config ${dockerconfig}  push ${imageName}"

    stage "Deploy"

        sh "sed 's#__IMAGE__#'$BUILDIMG'#' applications/k8s-cicd-nginx-test/k8s/deployment.yaml | kubectl --kubeconfig ${kubeconfig} apply -f -"
        sh "kubectl --kubeconfig ${kubeconfig} rollout status deployment/${appName}"
}
