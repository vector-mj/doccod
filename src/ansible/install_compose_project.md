# Install docker compose project

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
