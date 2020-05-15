# GitLab CI/CD
Record the flow on how gitlab CI/CD works with kubernetes.

Refer to [Gitlab CI/CD Doc](https://docs.gitlab.com/ee/ci/)

# Infrastructure
### Gitlab-runner 
 - Used to run your jobs and send the results back to GitLab.

# Watch Out
- Set ***~/.kube/config or KUBECONFIG env*** variable (ex. LAB3_KUBECONFIG) to the repository, which enables gitlab to send commands to K8s server.
  - k8s user profile

- You must generate ***Deploy Tokens*** to allow read-only access to your repository and registry images.
  - Settings → CI/CD → Deploy Tokens 

- Check .gitlab-ci.yml by gitLab CiLint

# Example of gitlab-ci.yml
```
variables:
  SERVER_IMAGE: $CI_REGISTRY_IMAGE/server:$CI_COMMIT_REF_SLUG
  DOCKER_DRIVER: overlay
  GIT_SUBMODULE_STRATEGY: recursive

services:
  - docker:dind

before_script:
  # login to gitlab docker registry
  - docker login ...

stages:
  - test
  - build
  - deploy
  
# -------------------
# test

test_variable:
  stage: test
  script:
    - echo $SERVER_IMAGE

# ---------------------------------------------------------------------------
# build

server-build:
  stage: build
  image: docker:latest
  only:
    - master
  script:
    - docker build -t $SERVER_IMAGE server/
    - echo $SERVER_IMAGE
    - docker push $SERVER_IMAGE

server-deploy-master:
  stage: deploy
  image: .../k8s-deploy:latest
  only:
    - master
  script:
    - export KUBECONFIG=$KUBECONFIG
    - sed -it "s/___ repace in k8s yaml/$CI_PIPELINE_ID/g" server/deploy/deploy.yaml
    - kubectl apply -f server/deploy/deploy.yaml -n user-yiming-chang

```
### Staging your pipeline
- test_variables (initialize)
- build
- deploy
- test (test service)

# CI Flow (.gitlab-ci.yml)
1. Set variables 
   - ***Registry Path - SERVER_IMAGE***
2. Using latest docker with dind
   - dind will create TLS (Transport Layer Security)
3. Build: create the docker image, push it to ***Registry Path (SERVER_IMAGE)***
4. Deploy: start to deploy on to k8s cluster by using the k8s-deploy image, then we start to create pods with deploy.yaml. 

***Continue on K8s' section - How to deploy on k8s?***


  