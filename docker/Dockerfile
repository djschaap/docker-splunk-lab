# Copyright 2019 Doug Schaapveld
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

FROM splunk/splunk:latest
USER root

# Install Java in case we want DB Connect
#   mkdir /usr/share/man/man1 is necessary due to
#   https://github.com/debuerreotype/docker-debian-artifacts/issues/24
RUN apt-get update \
  && mkdir -p /usr/share/man/man1 > /dev/null \
  && apt-get install -y libjtds-java libmysql-java openjdk-8-jre-headless \
  && apt-get clean

# Docker creates /tmp/defaults if bind-mounting /tmp/defaults/default.yml.
# Ansible requires that directory be writable by the "splunk" user.
RUN ( test -d /tmp/defaults || mkdir -m 0755 /tmp/defaults ) \
  && chown splunk /tmp/defaults

# create dummy_a Ansible role
# ${SPLUNK_ANSIBLE_HOME} is set from splunk/splunk image
RUN mkdir -m 0555 ${SPLUNK_ANSIBLE_HOME}/roles/dummy_a
COPY roles/dummy_a ${SPLUNK_ANSIBLE_HOME}/roles/dummy_a
COPY lab_plays.yml ${SPLUNK_ANSIBLE_HOME}

# package included dummy_b app, removing source files from image
#   (need to use multi-layer build to completely remove....)
COPY apps/dummy_b /apps/dummy_b
RUN (cd apps && tar czf /tmp/dummy_b.tgz dummy_b ) \
  && rm -rf /apps/dummy_b

# ${ANSIBLE_USER} is set from splunk/splunk image
USER ${ANSIBLE_USER}

ENV TZ=UTC
