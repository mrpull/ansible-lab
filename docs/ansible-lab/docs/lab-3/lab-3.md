# Advanced Role Playing
Ansible [roles](https://docs.ansible.com/ansible/latest/user_guide/playbooks_reuse_roles.html) allow you to remove bulky configuration out of the playbook, and into a folder structure. The entire goal of this is to make things modular, so that you can share and reuse roles, along with not having playbooks that are thousands of lines long.

A role might be created to manage a particular application on a particular kind of server (e.g. the SMTP service on mail servers) or to deploy a common application or setting to all servers.  Role creation may be assigned to developers on different teams (database vs. application serever).  Separation of responsibilities in roles also allows Ansible playbooks to be maintained independently based on skillsets or knowledge domains.

Role syntax is similar to playbooks, but the pieces are broken into a directory and file structure.

Roles expect files to be in certain directories. Roles must include at least one of these directories, however it is perfectly fine to exclude any which are not used. When in use, each directory must contain a main.yml file, which contains the relevant content:

* `tasks` - contains the main list of tasks to be executed by the role.
* `handlers` - contains handlers, which may be used by this role or even anywhere outside this role.
* `defaults` - default variables for the role.
* `vars` - other variables for the role.
* `files` - contains files which can be deployed via this role.
* `templates` - contains templates which can be deployed via this role.
* `meta` - defines some meta data for this role.


**This lab is only a high overview of roles.  More details can be found in the Ansible [documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_reuse_roles.html).**


# The Web Role
Reviewing existing examples is a good way to learn roles.  The `web` role only contains handlers, tasks, and templates directories.  Tasks and handlers directories contain a `main.yml` file that is the entry point for each folder.  More complex roles will break down tasks into smaller chunks and use the `main.yml` file to `import_tasks` from other files.

The `web` role in this lab is structured like this:

```bash
web
├── handlers
│   └── main.yml
├── tasks
│   └── main.yml
└── templates
    ├── default-site.j2
    ├── index.html.j2
    └── nginx.conf.j2
```

The `roles/web/tasks/main.yml` file contains a YAML formatted list of tasks.  Its contents will look familiar.  It does not include `hosts:`, `become:`, `gather_facts:`, etc. that you have seen in playbooks.

```yaml
---
- name: install nginx
  apt:
    name: nginx
    state: present

- name: write our nginx.conf
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
  notify: restart nginx

- name: write our /etc/nginx/sites-available/default
  template:
    src: default-site.j2
    dest: /etc/nginx/sites-available/default
  notify: restart nginx

- name: deploy website content
  template:
    src: index.html.j2
    dest: /usr/share/nginx/html/index.html
```

Playbooks are still used to execute roles.  The playbook `3-role-site.yml` is similar to `2-1-site.yml`.  It still executes three plays (each for common, web, and load balancer) but has a `roles:` section instead of `tasks`.

```yaml
---

# common
- hosts: all
  become: yes
  gather_facts: no

  roles:
    - common

# web
- hosts: web
  become: yes

  roles:
    - web

# lb
- hosts: lb
  become: yes

  roles:
    - lb
```
Run the playbook to see if any changes are observed.

```bash
$ ansible-playbook 3-role-site.yml
```

While the responsibilities of different plays are now broken into roles, the end result should be the same.

# Reusable Roles

Making roles reusable should be a goal.  Consideration should be taken if the role will be used in Red Hat Linux vs. Ubuntu or even Windows.  Roles provide flexibility to accommodate many types of reuse.  Some interesting examples of more complex roles are:

* Install Docker: [https://github.com/geerlingguy/ansible-role-docker](https://github.com/geerlingguy/ansible-role-docker)
* Install New Relic Infrastructure: [https://github.com/newrelic/infrastructure-agent-ansible](https://github.com/newrelic/infrastructure-agent-ansible)

[Ansible Galaxy](https://galaxy.ansible.com/) is a site for sharing roles you create or consuming ready made roles.

# Just the beginning

This lab only scratches the surface of what can be done with Ansible.  However, you should be comfortable with the basic concepts and be able to experiment further.

[Lab4](../lab-4/lab-4.md) outlines (but doesn't deep dive into) a rolling update of the content of the web servers.

More resources can be found in the [More](../more/more.md) section of this lab.

If you are finished, shut down the Vagrant environment like this:

```bash
# Stop VM's
$ vagrant halt
# Stop and remove from disk
$ vagrant destroy
```
You can do a `vagrant up` to start up a halted environment or recreate a destroyed environment.