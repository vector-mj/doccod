---
title: Gitlab runner for local environment
date: 2020-09-16
tags: ["git"]
cover:
  image: "/images/gitlab.png"
  # can also paste direct link from external site
  # ex. https://i.ibb.co/K0HVPBd/paper-mod-profilemode.png
  alt: "<alt text>"
  caption: "<text>"
  relative: false # To us
---

### Run Gitlab runner for local CI/CD
```bash
{
    docker run 
    --entrypoint bash
    --rm
    -w $PWD
    -v $PWD:$PWD
    -v /var/run/docker.sock:/var/run/docker.sock
    gitlab/gitlab-runner:latest
    -c 'git config --global --add safe.directory "*";gitlab-runner exec docker build'
}

```