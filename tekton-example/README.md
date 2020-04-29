# Tekton Pipeline/Task Example  

This example requires a Tekton Pipelines Beta installation (version 0.11.x or higher).

A small example of using `promote` in a Tekton Pipeline to promote a service's config to a GitOps repository.  Creation of the
PipelineRun (using `service-promote-pipeline-run.yaml`) will drive the pipeline to clone, build and push the service and then promote the config from the local clone into your staging/test GitOps repository.

Creation of a TaskRun (using `promote-run.yaml`) will then further promote from one GitOps repo to another, e.g. from
staging to production.

## Template Files

- `auth.yaml`: Creates secrets for GitHub and Docker registry and Create the ServiceAccount
- `gitconfig`: Data file for the configmap  
- `promote-secret.yaml`: Creates an access token secret for the GitHub repository
- `resources.yaml`: Creates PipelineResources for GitHub and Docker repositories
- `promote.yaml`: Creates a pull request from one repository to another repository

## Other Files
- `service-promote.yaml`: Creates a promote task which promotes from service repo to env repo
- `service-promote-pipeline.yaml`: Creates a pipeline that executes build, push and promote
- `service-promote-pipeline-run.yaml`: Creates a PipelineRun that executes the `service-promote-pipeline`
- `promote-run.yaml`: Creates a TaskRun that executes a promote task
- `build-task.yaml`: Creates a build push task

## Create Tekton Resources

- Create a new namespace:
```shell
kubectl create ns <namespace>
```

- Apply the resources folder:
```shell 
kubectl apply -f resources -n <namespace> 
```

- Edit all template files to contain real values. Entries of the form `<some property>` must be replaced with the value you wish to use, i.e at occurences such as `<image name>`, `<github org>/<github repo>` etc...

- Apply the templates folder:
```shell 
kubectl apply -f templates -n <namespace>
```

- Create a configmap:
```shell
kubectl create configmap promote-configmap --from-file=gitconfig -n <namespace>
```

## Execute Pipeline

`service-promote-pipeline-run` is designed to build your microservice from its development repository and then promote the new configuration to a GitOps repository (some dev/staging/test environment).

- Create `service-promote-pipeline-run`:
```shell
kubectl create -f service-promote-pipeline-run.yaml -n <namespace>
```

## Promote to Prod

You can use `promote-run.yaml` to run a subsequent promote from one GitOps repository to another (e.g staging to prod) after merging the pull request on your first GitOps repository.

- Create the `promote-run` TaskRun:
```shell
kubectl create -f promote-run.yaml -n <namespace>
```
