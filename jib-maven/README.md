# Jib Maven

This Task builds Java/Kotlin/Groovy/Scala source into a container image using Google's [Jib](https://github.com/GoogleContainerTools/jib) tool.

Jib works with [Maven](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin) and [Gradle](https://github.com/GoogleContainerTools/jib/tree/master/jib-gradle-plugin) projects, and this template is for Maven projects.

## Install the Task

```
kubectl apply -f https://raw.githubusercontent.com/tektoncd/catalog/master/jib-maven/jib-maven.yaml
```

## Inputs

### Parameters

- **DIRECTORY**: The directory in the source repository where source should be found. (*default: .*)
- **CACHE**: The name of the volume for caching Maven artifacts and base image layers (*default: empty-dir-volume*)

### Resources

* **source**: A `git`-type `PipelineResource` specifying the location of the
  source to build.

## Outputs

### Resources

* **image**: The Docker image name to apply to the newly built image.

## Usage

This TaskRun runs the Task to fetch a Git repo, and build and push a container
image using Jib (Maven)

```
apiVersion: tekton.dev/v1alpha1
kind: PipelineResource
metadata:
  name: example-image
spec:
  type: image
  params:
  - name: url
    value: gcr.io/tekton-task-project/my-image
```

```
apiVersion: tekton.dev/v1alpha1
kind: TaskRun
metadata:
  name: example-jib-maven
spec:
  taskRef:
    name: jib-maven
  inputs:
    params:
    - name: DIRECTORY
      value: ./examples/helloworld
    resources:
    - name: source
      resourceSpec:
        type: git
        params:
        - name: url
          value: https://github.com/my-user/my-repo
  outputs:
    resources:
    - name: image
      resourceRef:
        name: example-image
```

If you would like to customize the container, configure the `jib-maven-plugin` in your `pom.xml`. 
See [setup instructions for Maven](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin#setup) for more information.