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

# Docker creates /tmp/defaults if bind-mounting /tmp/defaults/default.yml.
# Ansible requires that directory be writable by the "splunk" user.
RUN ( test -d /tmp/defaults || mkdir -m 0755 /tmp/defaults ) \
  && chown splunk /tmp/defaults

# ${ANSIBLE_USER} is set from splunk/splunk image
USER ${ANSIBLE_USER}

ENV TZ=UTC
