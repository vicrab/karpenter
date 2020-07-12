# Karpenter

Tekton tasks to use in CI/CD pipelines.

## Tasks

The following tasks are available to use:

* git
* mvn
* buildah
* s2i
* kubectl

## Demo

The following demo consists on a sample pipeline to demonstrate the use of Karpenter tasks and Tekton pipelines.

### Run on Minikube

Start a Minikube instance:

    minikube start --memory=8g --insecure-registry "example.org"

Install the registry and registry-aliases addons:

    minikube addons enable registry
    minikube addons enable registry-aliases

Install Tekton pipelines and Tekton dashboard

    kubectl apply -f https://storage.googleapis.com/tekton-releases/pipeline/latest/release.yaml
    kubectl apply -f https://github.com/tektoncd/dashboard/releases/latest/download/tekton-dashboard-release.yaml

Create a namespace to deploy the application:

    kubectl create namespace hello

Create the tasks needed by the pipeline:

    kubectl apply -R -f ./tasks -n hello

Create the pipeline resources:

    kubectl apply -f ./demo/pipeline.yaml -n hello

Finally run the pipeline:

    echo "apiVersion: tekton.dev/v1beta1
    kind: PipelineRun
    metadata:
      name: hello-pipeline-run
    spec:
      serviceAccountName: build-bot
      pipelineRef:
        name: hello-pipeline
      workspaces:
      - name: source
        persistentvolumeclaim:
          claimName: source" | kubectl replace -f - -n hello

### Pipeline Overview

![pipelines](./demo/pipeline.png)

