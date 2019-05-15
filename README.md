# docker-splunk-lab
Demonstrates customization of (GitHub) splunk/docker-splunk image.

USE AT YOUR OWN RISK.

Project Home: https://github.com/djschaap/docker-splunk-lab

Docker Hub: https://cloud.docker.com/repository/docker/djschaap/docker-splunk-lab

## Makefile targets

- Makefile from https://github.com/mvanholsteijn/docker-makefile

```
make patch-release	increments the patch release level, build and push to registry
make minor-release	increments the minor release level, build and push to registry
make major-release	increments the major release level, build and push to registry
make release		build the current release and push the image to the registry
make build		builds a new version of your Docker image and tags it
make snapshot		build from the current (dirty) workspace and pushes the image to the registry
make check-status	will check whether there are outstanding changes
make check-release	will check whether the current directory matches the tagged release in git.
make showver		will show the current release tag based on the directory content.
make shell		build, then start container and run /bin/bash
make test		build, then start container and run tests
```

## Container Deployment

### Basic Deployment

- if using locally-built image, set DOCKERHUB_USER manually

```
DOCKERHUB_USER=djschaap
SPLUNK_PASSWORD=ChangeMe
docker run -d --rm -p 8000:8000 \
  -e SPLUNK_ANSIBLE_POST_TASKS=file:///opt/ansible/lab_plays.yml \
  -e SPLUNK_PASSWORD \
  -e SPLUNK_START_ARGS=--accept-license \
  --name splunk ${DOCKERHUB_USER}/docker-splunk-lab:latest
```

### Enhanced Deployment

- if using locally-built image, set DOCKERHUB_USER manually
- enables HEC on 8088/tcp with SSL
- note that local permissions for default.yml must allow the ansible user
  (UID determined within the container) to read the file

```
DOCKERHUB_USER=djschaap
docker run -it --rm splunk/splunk create-defaults > default.yml
# edit default.yml as desired
docker run -d --rm -p 8000:8000 -p 8088:8088 \
  -e "ANSIBLE_EXTRA_FLAGS=--extra-vars=@/tmp/defaults/default.yml" \
  -e SPLUNK_ANSIBLE_POST_TASKS=file:///opt/ansible/lab_plays.yml \
  -e SPLUNK_START_ARGS=--accept-license \
  --mount type=bind,src=`pwd`/default.yml,dst=/tmp/defaults/default.yml \
  --name splunk ${DOCKERHUB_USER}/docker-splunk-lab:latest
# retrieve admin password
grep '  password:' default.yml
# retrieve HEC token
grep '  hec_token:' default.yml
```

### UI Access

The splunkd UI runs on port 8000 by default ( http://localhost:8000 ).
The default username is "admin".

### Image Environment Variables

#### SPLUNK_PASSWORD

From upstream (Docker Hub) splunk/splunk image.
Password for "admin" user.
Must be at least 8 characters.

#### SPLUNK_START_ARGS

From upstream (Docker Hub) splunk/splunk image.
Set to "--accept-license" for best results.

## See Also

- https://github.com/splunk/docker-splunk
- https://github.com/mvanholsteijn/docker-makefile

## Development

### To re-apply Ansible Playbook (within a running container)

```
docker exec -it splunk /bin/bash
cd /opt/ansible
ansible-playbook -i inventory/environ.py site.yml -e @/tmp/defaults/default.yml
```
