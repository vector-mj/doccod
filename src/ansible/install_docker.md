# Install docker

```yaml
- name: Create directory for Docker's GPG key
  ansible.builtin.file:
    path: /etc/apt/keyrings
    state: directory
    mode: '0755'
  tags:
    - apt_install_docker

- name: Set DNS nameservers in /etc/resolv.conf
  become: true
  blockinfile:
      path: /etc/resolv.conf # BeGoZar
      block: |
            nameserver 185.55.226.26 
            nameserver 185.55.225.25
  tags:
    - apt_install_docker

- name: Add Docker's official GPG key
  ansible.builtin.apt_key:
    url: https://download.docker.com/linux/ubuntu/gpg
    keyring: /etc/apt/keyrings/docker.gpg
    state: present
  tags:
    - apt_install_docker
  retries: 10
  delay: 1

- name: create docker repo url
  ansible.builtin.shell:
    cmd: echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable"
  register: docker_apt_source
  tags:
    - apt_install_docker

- name: add docker repository to sources.list.d
  ansible.builtin.apt_repository:
    repo: "{{ docker_apt_source.stdout }}"
    state: present
    filename: docker
  tags:
    - apt_install_docker

- name: Update and upgrade all packages to the latest version
  ansible.builtin.apt:
    update_cache: yes
    force: true
  tags:
    - apt_install_docker

- name: Set DNS nameservers in /etc/resolv.conf
  become: true
  blockinfile:
      path: /etc/resolv.conf # Arvan SNI
      block: |
            nameserver 185.206.92.250
            nameserver 185.231.181.206
  tags:
    - apt_install_docker

- name: Install Docker and related packages
  ansible.builtin.apt:
    name: "{{ item }}"
    state: present
  loop:
    - docker-ce
    - docker-ce-cli
    - containerd.io
    - docker-buildx-plugin
    - docker-compose-plugin
  tags:
    - apt_install_docker
    
- name: Add Docker group
  ansible.builtin.group:
    name: docker
    state: present
  tags:
    - apt_install_docker
    
- name: Add user to Docker group
  ansible.builtin.user:
    name: "{{ ansible_user }}"
    groups: docker
    append: true
  tags:
    - apt_install_docker

- name: Enable and start Docker services
  ansible.builtin.systemd:
    name: "{{ item }}"
    enabled: true
    state: started
  loop:
    - docker.service
    - containerd.service
  tags:
    - apt_install_docker


```