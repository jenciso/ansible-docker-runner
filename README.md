## Gitlab Runner

### Intro 

This is a Gitlab Runner provision project based on ansible and gitlab-ci.

### Build

We are using the official image and no use a custom image

```shell
docker pull gitlab/gitlab-runner:latest
```

### Deploy 

#### Prerequisites 

Minimum 2 hosts:

- runner-deploy  (2GB RAM) - Runner to deploy artifacts
- runner-build   (2GB RAM) - Runner to build artifacts

#### One simple Runner

```shell
docker run \
  -d --name gitlab-runner --restart always \
  -v /srv/gitlab-runner/config:/etc/gitlab-runner \
  -v /var/run/docker.sock:/var/run/docker.sock \
  gitlab/gitlab-runner:latest
```
> Later you need to register it

#### Multiple Runners using differents ID's

* Export some variables

```shell
export REGISTRATION_TOKEN=MY_GITLAB_TOKEN
export GITLAB_URL=https://gitlab-lab.docs-planet.site/
export id=$(date | md5sum | head -c 6)
```

* Register the runner

```shell
docker run --rm -v /etc/gitlab-runner/runner-$id:/etc/gitlab-runner \
  gitlab/gitlab-runner register \
  --non-interactive \
  --executor "docker" \
  --docker-image alpine:latest \
  --docker-volumes "/var/run/docker.sock:/var/run/docker.sock" \
  --docker-privileged=true \
  --url "${GITLAB_URL}" \
  --registration-token "${REGISTRATION_TOKEN}" \
  --description "docker-runner" \
  --tag-list "build,shared" \
  --run-untagged="true" \
  --locked="false" \
  --access-level="not_protected"
```

* Start the runner

```shell
docker run -d \
  --name gitlab-runner-$id --restart always \
  -v /etc/gitlab-runner/runner-$id:/etc/gitlab-runner \
  -v /var/run/docker.sock:/var/run/docker.sock \
  gitlab/gitlab-runner:latest
```

* Stop the runner

```
docker stop gitlab-runner-$id
docker rm gitlab-runner-$id
```
			
* Unregister the runner

```shell
docker run --rm -v /etc/gitlab-runner/runner-$id:/etc/gitlab-runner \
  gitlab/gitlab-runner unregister --all-runners
```

* Delete config

```shell
rm -rf /etc/gitlab-runner/runner-$id
```
