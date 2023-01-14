

## 1 Introduction

It is now time to use GitLab for something other than hosting GIT repositories. In this short Lab, you will discover how to make your GitLab project use your K8S cluster in order to automatically trigger tests, build images and deploy services on your cluster.

Continuous integration as well as continuous deployment and delivery are intended to eliminate tedious tasks for programmers (running tests, building and pushing docker images) and make the deployment process more reliable. More precisely, in the context of your project, **Continuous Integration** (CI) is to automate the testing of code when you commits/pushes change to GitLab. **Continuous Delivery**means automatic generation of docker images for the services of the application; the images are automatically pushed to the registry used by the Kubernetes deployments. With **Continuous Deployment**, the application code is deployed on the Kubernetes cluster.

## 2 Kubernetes Integration

One thing to do, as far as configuring Gitlab is concerned, is to integrate your Kubernetes cluster into your GitLab project. This has already been done by the teachers.

Follow these instructions to check if everything is OK:

- Log in to GitLab and select your project
- Select **Infrastructure/Kubernetes clusters** from the panel on the left side of the window

You should see in the central window the name of your cluster: `k8s-agent`. You can click on the name to see the Integration Status (it should be **Connected**).

## 3 Setting a Kubernetes GitLab Runner

As we will see in more detail later in this text, GitLab CI/CD requires the execution of jobs (to test code, build docker images and deploy services). GitLab does not execute these jobs directly; it delegates their execution to **GitLab Runners**. There are several ways to set up a runner capable of running the jobs for your project. Here, you will use your Kubernetes cluster to run these jobs.

Go to your project's **Settings>CI/CD** and expand the **Runners** section. If everything was done correctly by the teachers, you should see a green disk next to the Runner ID under the heading Available specific runners on the user interface of GitLab.

## 4 Mongo Databases

So far, you have been working with a Mongo server running on your local VM using a Docker container. We are now approaching the phase that will involve deploying the services (`photographer`, `photo`, `tags` as well as the one you define) on your Kubernetes cluster. It is therefore obviously no longer possible to continue using a Mongo container on your local VM.

### 4.1 Mongo Server: mongo.cloud.rennes.enst-bretagne.fr

You will all use a Mongo server hosted at the following address: `mongo.cloud.rennes.enst-bretagne.fr`.

The connection parameters to the Mongo server are materialized by the attributes of the Settings class: `mongo_host`, `mongo_user`, `mongo_password` and `database_name` (see source code for `photographer` service). When you run the `photographer` service (or `photo` service), you can use an environment variable to define a value different from the one set in the source code for each of these parameters. You did it for example, in the REST lab, to tell the container running the `photographer` service the name (or IP address) of the `mongo` server. Since you are now going to use Kubernetes, you will obviously not use the `--env` docker option to set the Mongo parameters. Instead, you will use an `env` field in the Kubernetes description of the POD.

Download the `k8s-photographer.yml` from http://www.cloud.rennes.enst-bretagne.fr/files/gitlab-lab.

As you can see, the name of the Mongo server as well as the name of the database to be used by the `photographer` service is specified in the K8S file describing the Kubernetes service. Of course, you will replace the name of the database with the one that corresponds to your group. As for the username and the associated password, we do not write their values directly in the K8S file (for obvious security reasons). As you can see, it is assumed that this username and password are stored in a K8S secret.

The secret has already been created by using the following command:

```
> kubectl create secret generic mongo --from-literal=username=xxx-user --from-literal=password=yourpassword
```

Thanks to the secret, you can upload the code of your service and the K8S file describing it on your GIT repository without revealing the password used to connect to the Mongo database.

## 5 Continuous Integration/Delivery/Deployment for the photographer Service

To enable CI/CD for a project, a `.gitlab-ci.yml` file must be present at the root directory of your repository. The `.gitlab-ci.yml` file tells the GitLab runner what to execute.

Download the `.gitlab-ci.yml` file from http://www.cloud.rennes.enst-bretagne.fr/files/gitlab-lab, and make sure you place it at the root of your repository.

As you can see, three stages are defined:

- test
- release
- deploy

Each stage contains precisely one job to be executed in the example provided. Indeed, as we provide you with this gitlab CI/CD file only for the `photographer` service, there is one test job, one release job and one deploy job. You will have to define other jobs for the other services of your application

### 5.1 Continuous Integration with GitLab

Continuous integration is for testing. Below, you will find the `yaml` description of the job for testing the `photographer` service. The name of the **job** is `test_photographer`.

```
test_photographer:
  stage: test
  variables:
    MONGO_HOST: mongo-test
  image: $CI_REGISTRY/$CI_PROJECT_PATH/photoapptest
  script:
  - cd app/photographer-service
  - pytest -p no:warnings
  services:
  - name: mongo:4.4.12
    alias: mongo-test
```

A job has a unique name; it is defined by a list of parameters that define the job behavior: the `image` parameter gives the name of the docker image to be used for running the code defined in the `script` parameter. The `services` parameter enumerates additional docker images to be run during a job; the created containers will be linked to the container executing the script. The `stage` parameter indicates to which stage the job belongs to; It allows to group jobs into different stages. A **pipeline** is a group of jobs that get executed in stages.

Why do we define a `MONGO_HOST` variable? Why do we use an `alias` field in the description of the `mongo` service required to run the `photographer` service test?

During the Rest Lab, you had prepared a docker image (`photoapptest`) to execute the tests for the `photographer` service. Tag and Push this image on your GitLab registry.

If the name of your test image is `photoapptest`, you can add the `.gitlab-ci.yml` under GIT version control and commit/push to GitLab. If you have changed the name of the test image, then change the value of the `image` field in the job description accordingly before commiting/pushing.

Connect to [http://gitlab.imt-atlantique.fr](http://gitlab.imt-atlantique.fr/) and select your projet. On the left hand pane, from the **CI/CD** menu, select the **Pipelines** entry. On the right hand pane, the **Pipelines** dashboard should appear. Find out for yourself how to display the console of your test execution.

### 5.2 Continuous Delivery with GitLab

Continuous integration is for generating the docker images of the application. When you define a job, you use the `image` keyword to specify the Docker image to be used for running the job. For the test, you have used an image you built yourself. For the `release` stage, you will use the following image: `gcr.io/kaniko-project/executor:debug`

```
build_photographer:
  stage: release
  image:
    name: gcr.io/kaniko-project/executor:debug
    entrypoint: [""]
  # These variables are needed to make executor image able to download images
  variables:
    HTTP_PROXY: http://proxy.enst-bretagne.fr:8080
    HTTPS_PROXY: http://proxy.enst-bretagne.fr:8080
    NO_PROXY: gitlab-devops.cloud.rennes.enst-bretagne.fr:4567
  before_script:
    - mkdir -p /kaniko/.docker
    - echo "{\"auths\":{\"${CI_REGISTRY}\":{\"auth\":\"$(printf "%s:%s" "${CI_REGISTRY_USER}" "${CI_REGISTRY_PASSWORD}" | base64 | tr -d '\n')\"}}}" > /kaniko/.docker/config.json
  script:
    - >-
      /kaniko/executor
      --context "${CI_PROJECT_DIR}/app/photographer-service"
      --dockerfile "${CI_PROJECT_DIR}/app/photographer-service/Dockerfile"
      --destination "${CI_REGISTRY_IMAGE}/photographer:${CI_COMMIT_SHORT_SHA}"
      --build-arg HTTP_PROXY=${HTTP_PROXY}
      --build-arg HTTPS_PROXY=${HTTPS_PROXY}

      # Again, HTTP(S)_PROXY needed for the docker build with kaniko
```

Previous versions of Kubernetes took care of installing Docker on the workers and it was then possible to use Docker-in-Docker images to build, tag and push Docker images from a Docker container. Those days are over because now **Docker is not installed** on the hosts used by Kubernetes. So we have to use an alternative solution and this one is offered to us by the kaniko project: https://github.com/GoogleContainerTools/kaniko.

We must admit that it is quite complicated to use because of:

- the generation of a json file (from the `CI_REGISTRY`, `CI_REGISTRY_USER`, `CI_REGISTRY_PASSWORD` variables) so that the container has the right to push images in the docker registry associated with your project (again, see `before_script` field).
- the need to use proxies to go out on the Internet to:
  1. download docker images (see `variables` field for `build_photographer`)
  2. allow the image build to go through (`--build-arg` option to `executor` in `script` field).

The GitLab variables used in the `build_photographer` stage are the following:

- `CI_REGISTRY_USER`, as its name suggests, contains the username that must be used to push images to the GitLab registry.
- `CI_REGISTRY_PASSWORD` contains the password used to authenticate against the GitLab registry. Of course, GitLab knows this password and so does the GitLab runner.
- `CI_REGISTRY` contains the address of the GitLab registry.
- `CI_PROJECT_DIR` contains the full path the repository is cloned to.
- `CI_COMMIT_SHORT_SHA` contains the first eight characters of the commit revision.

Thanks to the `CI_COMMIT_SHORT_SHA`, that we will reuse in the k8s file, we will be able to reference the last image built during the deployment stage. 

Make sure that the `build_photographer` job runs correctly using your GitLab project interface.

### 5.3 Continuous Deployment with GitLab

```
deploy_photographer:
  stage: deploy
  image:
    name: bitnami/kubectl
    entrypoint: [""]
  script:
    - cd app/photographer-service
    - sed -i "s/:latest/:${CI_COMMIT_SHORT_SHA}/g" k8s-photographer.yml
    - kubectl apply -f k8s-photographer.yml
  environment:
    name: production
```

Continuous deployment is for deploying the appliction code on your Kubernetes cluster. For the `deploy` stage, we use the following image: `bitnami/kubectl` (from the Docker Hub). This image allows to use the `kubectl` CLI in the script associated with the `deploy_photographer` job.

As you can see, we replace the name of the image `photographer:latest` with `photographer:${CI_COMMIT_SHORT_SHA}`in order to be sure to deploy the version of the image that was just built.

Make sure that the `deploy_photographer` job runs correctly 1) by using your GitLab project interface and 2) by checking the status of the `photographer` POD with `kubectl`.

## 6 Continuous Integration/Delivery/Deployment for the Photo Sharing Application

We have given you in the previous section all that is needed to test, build the Docker images and deploy the `photographer` service. Of course, your application is made up of other services and you now need to define the CI/CD jobs associated with these services.

Write already the `YAML` description of the jobs associated with the `photo` and `tag` services. Once you have made progress in the implementation of the service you are working on, you can also write the `YAML` description of the jobs to test, build and deploy it.

Author: Alberto Blanc, Jean-Pierre Le Narzul

Created: 2023-01-12 Thu 22:04

[Validate](https://validator.w3.org/check?uri=referer)