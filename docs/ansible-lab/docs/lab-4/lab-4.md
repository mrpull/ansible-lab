# Are you ready?
* If you haven't already, scale your Vagrant web farm to 6 virtual machines.
* Make sure all nodes are configured for password-less logins
* Run `ansible -m ping all` to test ansible connectivity
* Run `ansible-playbook 3-role-site.yml` to get environment to known good configuration
* Browse to `http://localhost:8080` and refresh a few times

# Rolling Updates
Users (and businesses) expect nearly 100% uptime of websites.  A rolling update, or updating each server one at a time, can provide a zero-downtime deployment.  Ansible can orchestrate these deployments.

# Serial Tasks
By default, Ansible runs tasks in parallel across hosts (up to the number in the forks setting in the \[default\] section of `ansible.cfg`).  In our rolling update scenario, we only want one web server at a time removed from the pool for updating.  The `serial: 1` setting of the web play will limit execution to one server at a time until all hosts in the group have been processed.

Skim through the playbook below to see the tasks.  See comments inline for new concepts.  This playbook isn't appropriate for production, but could be good for future inspiration.

The shell commands that disable and enable servers in the load balancer pools are ugly, but should do the trick.

```yaml
---

# common
- hosts: all
  become: yes
  gather_facts: no

  tasks:

  - name: install git
    apt:
      name: git
      state: present
      update_cache: yes

# web
- hosts: web
  become: yes
  vars:
    app_version: release-0.01
  # Limit execution to one node at a time
  serial: 1

  # Tasks to execute before roles or main tasks section.
  pre_tasks:

  - name: disable server in haproxy
    shell: echo "disable server ansible-lab/{{ inventory_hostname }}" | socat stdio /var/lib/haproxy/stats
    delegate_to: "{{ item }}"
    with_items: "{{ groups.lb }}"

  tasks:

  - name: install nginx
    apt:
      name: nginx
      state: present

  - name: write our nginx.conf
    template:
      src: templates/nginx.conf.j2
      dest: /etc/nginx/nginx.conf
    notify: restart nginx

  - name: write our /etc/nginx/sites-available/default
    template:
      src: templates/default-site.j2
      dest: /etc/nginx/sites-available/default
    notify: restart nginx

  - name: clean existing website content
    file:
      path: /usr/share/nginx/html/
      state: absent

  # The git module will pull requested version from git
  - name: deploy website content
    git:
      repo: https://github.com/mrpull/episode-47.git
      dest: /usr/share/nginx/html/
      version: "{{ app_version }}"

  handlers:

  - name: restart nginx
    service:
      name: nginx
      state: restarted

  # Tasks to execute after roles or main tasks section.
  post_tasks:

  - name: enable server in haproxy
    shell: echo "enable server ansible-lab/{{ inventory_hostname }}" | socat stdio /var/lib/haproxy/stats
    delegate_to: "{{ item }}"
    with_items: "{{ groups.lb }}"

# lb
- hosts: lb
  become: yes

  tasks:

  - name: Download and install haproxy and socat
    apt:
      name: "{{ item }}"
      state: present
    with_items:
    - haproxy
    - socat

  - name: Enable HAProxy
    lineinfile:
      path: /etc/default/haproxy
      regexp: "^ENABLED"
      line: "ENABLED=1"
    notify: restart haproxy

  - name: Configure the haproxy cnf file with hosts
    template:
      src: templates/haproxy.cfg.j2
      dest: /etc/haproxy/haproxy.cfg
    notify: restart haproxy

  handlers:

  - name: restart haproxy
    service:
      name: haproxy
      state: restarted
```

Run the playbook while frequently refreshing your browser on [http://localhost:8080](http://localhost:8080).  You may also want to browse to and refresh [http://localhost:8080/haproxy?stats](http://localhost:8080/haproxy?stats) to see how the HAProxy changes throughout the playbook.

```bash
$ ansible-playbook 4-rolling.yml -e "app_version=release-0.01"
# Run again with a new version
$ ansible-playbook 4-rolling.yml -e "app_version=release-0.02"
# Run again with another new version
$ ansible-playbook 4-rolling.yml -e "app_version=release-0.03"
```
A parallel deployment may be faster, but the rolling update helps avoid user interruption.

# Go Forth And Automate

If you are finished, shut down the Vagrant environment like this:

```bash
# Stop VM's
$ vagrant halt
# Stop and remove from disk
$ vagrant destroy
```
You can do a `vagrant up` to start up a halted environment or recreate a fresh environment after a destroy.

More resources can be found in the [More](../more/more.md) section of this lab.