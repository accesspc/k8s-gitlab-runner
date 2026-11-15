# gitlab-runner

[![pipeline status](https://gitlab.com/reiciunas/k8s/gitlab-runner/badges/main/pipeline.svg)](https://gitlab.com/reiciunas/k8s/gitlab-runner/-/pipelines)
[![coverage report](https://gitlab.com/reiciunas/k8s/gitlab-runner/badges/main/coverage.svg)](https://gitlab.com/reiciunas/k8s/gitlab-runner/-/commits/main)
[![Latest Release](https://gitlab.com/reiciunas/k8s/gitlab-runner/-/badges/release.svg)](https://gitlab.com/reiciunas/k8s/gitlab-runner/-/releases)

## Docker host

* Navigate to [Gitlab groups](https://gitlab.com/dashboard/groups)
* Choose group
* Menu -> Build -> Runners
* Click Create group runner
* Fill in form details:
  * Tags: `ci,shared`
  * Create runner
* Copy **runner authentication token** (starting with `glrt-`)
* Exec into gitlab-runner container
* Run the following commands

```sh
# Create docker volume
docker volume create gitlab-runner-config

# Start gitlab-runner container
docker run -d --name gitlab-runner --restart always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v gitlab-runner-config:/etc/gitlab-runner \
  gitlab/gitlab-runner:latest

# Exec into gitlab-runner container
docker exec -it gitlab-runner bash

# Run registration command, first replace _TOKEN_ with gitlab token from gitlab.com
gitlab-runner register \
  --non-interactive \
  --docker-image=docker:latest \
  --docker-privileged \
  --docker-volumes=/runner/services/docker \
  --executor=docker \
  --request-concurrency 3 \
  --url https://gitlab.com \
  --token _TOKEN_

sed -s -i 's/concurrent = 1/concurrent = 3/g' /etc/gitlab-runner/config.toml
```

* Confirm new runner is available and green
