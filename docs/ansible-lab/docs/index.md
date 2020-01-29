# What Is Ansible?

Ansible Engine is a tool used for automation, provisioning, app deployment, configuration management, and orchestration.  Ansible Engine is open source and is sponsored by Red Hat.  It is the core of a bigger suite of tools that includes Ansible Tower and Red Hat Automation.

More information about Ansible is [here](https://www.ansible.com/overview/it-automation).

# No Agents

An Ansible `Control Node`connects via SSH, WinRM, and HTTPS protocols to `Managed Nodes` or `hosts`.  Unlike SaltStack, Puppet, etc., no agent is required.  Ansible is not installed on managed nodes.  Firewall changes are often not required since it uses the same protocols used for interactive maintenance.

# Inventories

Ansible inventories are (by default) an .INI-style file that contains the names of managed nodes and allows grouping into classes such as `webservers`, `database` or environments like `production` and `qa` or by location such as `us-west1` or `us-east1`.  There is also a special group `all`.

Inventories can also pull in hosts from AWS, VMware, or scripts.

# Ad Hoc (and Parallel) Task Execution

Ansible can be used to run one-off commands on one or many managed nodes (e.g. check free disk space with `df -h`) or to execute one of Ansible's hundreds of built in modules (e.g. `ansible -m user -a "user=joedoe state=absent"`)

# Modules

Ansible Modules are pieces of code (usually written in Python or PowerShell) used to accomplish a particular function like configure a user account, install a package, or verify a config file is in compliance. Hundreds of modules are included with Ansible.  Modules are also fairly easily written to add functionality to Ansible.

The directory of standard modules is [here](https://docs.ansible.com/ansible/latest/modules/modules_by_category.html#modules-by-category).

# Tasks

Tasks are a unit of work.  A task may be a single one off command or many tasks can be combined into a sequence of work to configure a system or automate a deployment.

# Playbooks
Playbooks are Ansible’s configuration, deployment, and orchestration language. They can describe a policy you want your remote systems to enforce, or a set of steps in a general IT process.

# Roles
Roles are a way to organize and compartmentalize related Ansible content including tasks, templates, and files.  They are used to make reusable components or building blocks.  A "common" role could be a collection of settings used by all servers and a more specific role could be used to configure a custom application.
