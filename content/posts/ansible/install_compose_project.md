
---
title: Ansible tasks to install compose project
date: 2024-10-22T10:26:41+00:00
tags: ["ansible","docker"]
cover:
  image: "/images/ansible.webp"
  # can also paste direct link from external site
  # ex. https://i.ibb.co/K0HVPBd/paper-mod-profilemode.png
  alt: "<alt text>"
  caption: "<text>"
  relative: false # To us
---

```yaml

- name: create project directory
  ansible.builtin.file:
    path: "{{ project_path }}"
    state: directory
    recurse: true

- name: copy deployment (Docker Compose) files
  copy:
    src: files/{{ item }}
    dest: "{{ project_path }}/{{ item }}"
  loop:
    - docker-compose.yaml
    - .env

- name: deploy Docker Compose stack
  community.docker.docker_compose_v2:
    project_src: "{{ project_path }}"
    state: present
    files:
      - docker-compose.yaml
  tags:
    - start
    
- name: stop Docker Compose stack
  community.docker.docker_compose_v2:
    project_src: "{{ project_path }}"
    state: absent
    files:
      - docker-compose.yaml
  tags:
    - never
    - stop
```