properties([
    parameters ([
        
        booleanParam(name: 'CLEAN_WORKSPACE', defaultValue: true, description: 'Clean the workspace at the end of the run')
    ]),
    pipelineTriggers([
            [$class: "GitHubPushTrigger"]
    ]),
    [$class: 'GithubProjectProperty', displayName: '', projectUrlStr: 'https://github.com/ossimlabs/omar-builder'],
    buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '3', daysToKeepStr: '', numToKeepStr: '20')),
    disableConcurrentBuilds()
])
podTemplate(
  containers: [
    containerTemplate(
      name: 'docker',
      image: 'docker:latest',
      ttyEnabled: true,
      command: 'cat',
      privileged: true
    )
  ],
  volumes: [
    hostPathVolume(
      hostPath: '/var/run/docker.sock',
      mountPath: '/var/run/docker.sock'
    ),
  ]
)

{

node(POD_LABEL){
    stage("Checkout branch $BRANCH_NAME")
    {
        checkout(scm)
    }
    String VERSION = readFile(file: './Version.txt')
    stage("Load Variables")
    {
      withCredentials([string(credentialsId: 'o2-artifact-project', variable: 'o2ArtifactProject')]) {
        step ([$class: "CopyArtifact",
          projectName: o2ArtifactProject,
          filter: "common-variables.groovy",
          flatten: true])
        }
        load "common-variables.groovy"
    }
    stage (" Build centos docker App")
    {
        container('docker') 
        {
            withDockerRegistry(credentialsId: 'dockerCredentials', url: "https://${DOCKER_REGISTRY_PUBLIC_UPLOAD_URL}") {
                sh """
                  docker build --network=host -t ${DOCKER_REGISTRY_PUBLIC_UPLOAD_URL}/ossim-deps-builder-centos:$VERSION ./centos
                """
            }
        }
    }

    stage (" Build alpine docker App")
    {
        container('docker') 
        {
            withDockerRegistry(credentialsId: 'dockerCredentials', url: "https://${DOCKER_REGISTRY_PUBLIC_UPLOAD_URL}") {
                sh """
                  docker build --network=host -t ${DOCKER_REGISTRY_PUBLIC_UPLOAD_URL}/ossim-deps-builder-alpine:$VERSION ./alpine
                """
            }
        }
    }

    stage ("Push centos docker App")
    {
        container('docker')
        {
            withDockerRegistry(credentialsId: 'dockerCredentials', url: "https://${DOCKER_REGISTRY_PUBLIC_UPLOAD_URL}") {
                sh """
                  docker push ${DOCKER_REGISTRY_PUBLIC_UPLOAD_URL}/ossim-deps-builder-centos:$VERSION
                """
            }
        }
    }

    stage ("Push alpine docker App")
    {
        container('docker')
        {
            withDockerRegistry(credentialsId: 'dockerCredentials', url: "https://${DOCKER_REGISTRY_PUBLIC_UPLOAD_URL}") {
                sh """
                  docker push ${DOCKER_REGISTRY_PUBLIC_UPLOAD_URL}/ossim-deps-builder-alpine:$VERSION
                """
            }
        }
    }

	stage("Clean Workspace"){
    if ("${CLEAN_WORKSPACE}" == "true")
      step([$class: 'WsCleanup'])
  }
}
}