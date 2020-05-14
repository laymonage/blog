+++
title = "Deploying to multiple environments through CI"
date = 2020-05-14T11:49:00+07:00
tags = ["ppl", "devops"]
toc = false
comments = true
draft = false
description = """
Continuous integration pairs nicely with continuous deployment.
"""
images = ["/images/uploads/deployment.png"]
+++

{{< figure src="/images/uploads/deployment.png"
height="640px"
caption="Deployments can be quite scary for some people."
attr="(PNG Guru)"
attrlink="https://www.pngguru.com/free-transparent-background-png-clipart-npifj" >}}

In a [previous post][compose-arch], I've described a microservices
architecture that's defined using Docker Compose. The next question
would be, how will that be deployed? Well, I've got you covered.
Thanks to continuous integration services, we can deploy to multiple
environments altogether, depending on the branch.

We're going to define the deployments in three stages: `setup`, `test`,
and `deploy`. The `deploy` stage will deploy our application into
two different environments: `staging` and `production`. The `staging`
environment will be deployed from the `staging` branch, while the
`production` environment will be deployed from the `production` branch.
We're going to use GitLab CI in this example, but it can be adapted
for other CI services as well.

Let's start with the following `.gitlab-ci.yml`:

```yml
image: node:lts

stages:
  - setup
  - test
  - deploy

setup-gateway:
  stage: setup
  before_script:
    - cd gateway
  script:
    - yarn
  only:
    changes:
      - .gitlab-ci.yml
      - gateway/**/*
    refs:
      - branches
  cache:
    key: gateway
    paths:
      - gateway/node_modules/

test-gateway:
  stage: test
  before_script:
    - cd gateway
  script:
    - yarn lint
    - yarn test
  only:
    changes:
      - .gitlab-ci.yml
      - gateway/**/*
    refs:
      - branches
  cache:
    key: gateway
    policy: pull
    paths:
      - gateway/node_modules/
  artifacts:
    paths:
      - gateway/coverage/
    expire_in: 1 hour
```

The above YAML defines the `setup` and `test` jobs. To keep it short, I
only include the jobs for the `gateway`. It needs to be duplicated for
`db-service` and `frontend` as well.

Now, let's move on to the deployment. We're using Heroku for our `staging`
environment. It's very straightforward. We just need to add another job
to deploy to Heroku. We're using the `dpl` tool to make deployments easier.

```yml
deploy-staging-gateway:
  stage: deploy
  image: ruby:latest
  before_script:
    - cd gateway
  script:
    - gem install dpl
    - dpl --provider=heroku --app=$HEROKU_APP_GATEWAY --api-key=$HEROKU_API_KEY
  only:
    changes:
      - .gitlab-ci.yml
      - gateway/**/*
    refs:
      - staging
  cache:
    key: gateway
    policy: pull
    paths:
      - gateway/node_modules/
```

As you can see, we need to provide the Heroku application name
(`$HEROKU_APP_GATEWAY`) and the API key (`$HEROKU_API_KEY`) so that `dpl`
knows where to deploy our application. You can provide those keys in the
CI settings.

Now, for the `production` environment. We're using a single DigitalOcean
droplet for this one. Let's add another job to deploy to the droplet.

```yml
deploy-production:
  image: docker/compose:alpine-1.25.5
  stage: deploy
  before_script:
    - "which ssh-agent || ( apk update && apk add openssh-client )"
    - eval $(ssh-agent -s)
    - echo "$SSH_PRIVATE_KEY" | tr -d '\r' | ssh-add - > /dev/null
    - mkdir -p ~/.ssh
    - chmod 700 ~/.ssh
    - ssh-keyscan "$SSH_IP" >> ~/.ssh/known_hosts
    - chmod 644 ~/.ssh/known_hosts
    - echo "$ENV_MYSQL" > ./.env
    - echo "$ENV_DB_SERVICE" > ./db-service/.env
    - echo "$ENV_GATEWAY" > ./gateway/.env
    - echo "$ENV_FRONTEND" > ./frontend/.env
  script:
    - DOCKER_HOST="ssh://$SSH_USER@$SSH_IP" docker-compose up -d --build
    - docker -H "ssh://$SSH_USER@$SSH_IP" exec db-service './migrate.sh'
  after_script:
    - rm ./.env
    - rm ./db-service/.env
    - rm ./gateway/.env
    - rm ./frontend/.env
  only:
    - master
```

To authenticate to the droplet, we use SSH. So, we need to provide the
private SSH key (`$SSH_PRIVATE_KEY`) and the droplet's IP (`$SSH_IP`).
You can also provide environment variables and put them into `.env` files.
After that, we just need to run `docker-compose up -d --build` to build
the images and start the containers in the background. We also added
another script to run the migrations for the database.

And, that's it! We've defined the configuration for our multi-environment
deployment setup. Hooray!

[compose-arch]: {{< ref "compose-arch.md" >}}
