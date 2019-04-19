lab install setup
1. 
yum list installed python
2. 
sudo yum install -y ansible

ansible --version

3.1 
cd /home/student
mkdir ~/dep-install && cd ~/dep-install

3.2 
cat > inventory <<EOF
[dev]
servera.lab.example.com
EOF

4. 
[dep-install]$ ansible dev -i inventory --list-hosts

5. lab install grade


Guided Exercise: Managing Ansible Configuration Files
===========================================================
[student@workstation -]$ lab manage setup

make sure you are on /home/student/
1. 
mkdir ~/dep-manage && cd ~/dep-manage


3 [3.1 - 3.4] 
[myself]
localhost

[intranetweb]
servera.lab.example.com

[everyone:children]
myself
intranetweb

4. [student@workstation dep-manage]
ansible myself --list-hosts
ansible intranetweb --list-hosts
ansible everyone --list-hosts


5 [5.1 -5.6]
ansible. cfg

[defaults]
inventory = ./inventory

[privilege_escalation]
become = true
become_method = sudo
become_user = root
become_ask_pass = true

6. ansible intranetweb --list-hosts -v
-- sudo password, enter student.

7. [student@workstation dep-manage]$ ansible everyone -m ping

+++++++++++++++++++++++++++++++++++++++++++++++++++++++++
Chapter:2  Guided Exercise: Running Ad Hoc Commands
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++
[student@workstation -]$ lab adhoc setup
[student@workstation dep -adhoc]$

1.1
[student@workstation -]$ sudo cat /etc/sudoers.d/devops
1.2 
[student@workstation -]$ ssh devops@servera.lab.example.com

2. 
[student@workstation -]$ 
pwd
/home/student/

cd /home/student/dep-adhoc

cat ansible.cfg

[defaults]
inventory=inventory

cat inventory
[myself]
localhost
[intranetweb]
servera.lab.example.com
[everyone:children]
myself
intranetweb

3. ansible everyone -m ping
4. ansible localhost -m command -a 'id'
5. ansible localhost -m command -a 'id' -u devops
6. ansible localhost -m command -a 'cat /etc/motd' -u devops
7. ansible localhost -m copy -a 'content="Managed by Ansible\n" dest=/etc/motd' -u devops
8. ansible localhost -m copy -a 'content="Managed by Ansible\n" dest=/etc/motd ' -u devops --become
9. ansible everyone -m copy -a 'content="Managed by Ansible\n" dest=/etc/motd' - u devops --become
10. ansible everyone -m command -a 'cat /etc/motd' -u devops

+++++++++++++++++++++++++++++++++++
Managing Dynamic Inventories
+++++++++++++++++++++++++++++++++++
[student@workstation -]$ lab deploy-dynamic setup
1. 
[student@workstation -]$ cd /home/student/dep-dynamic
2. 
[defaults]
inventory=inventory

3. [student@workstation dep-dynamic]$ mkdir inventory
4.
wget http://materials.example.com/dynamic/ inventorya.py -0 inventory/inventorya.py

---
#!/usr/bin/env python

from subprocess import Popen,PIPE
import sys
import json

result = {}
result['webservers'] = {}
result['webservers']['hosts'] = []
result['webservers']['vars'] = {}

pipe = Popen(['getent', 'hosts'], stdout=PIPE, universal_newlines=True)

for line in pipe.stdout.readlines():
   s = line.split()
   if s[1].startswith('servera'):
      result['webservers']['hosts'].append(s[1])

if len(sys.argv) == 2 and sys.argv[1] == '--list':
    print(json.dumps(result))
elif len(sys.argv) == 3 and sys.argv[1] == '--host':
    print(json.dumps({}))
else:
    print("Requires an argument, please use --list or --host <host>")
----


wget http://materials.example.com/dynamic/ inventoryw.py -0 inventory/inventoryw.py

========++++++++++++++++ ===========

#!/usr/bin/env python

from subprocess import Popen,PIPE
import sys
import json

result = {}
result['all'] = {}

pipe = Popen(['getent', 'hosts'], stdout=PIPE, universal_newlines=True)

result['all']['hosts'] = []
for line in pipe.stdout.readlines():
   s = line.split()
   if s[1].startswith('workstation'):
      result['all']['hosts'].append(s[1])

result['all']['vars'] = {}

if len(sys.argv) == 2 and sys.argv[1] == '--list':
    print(json.dumps(result))
elif len(sys.argv) == 3 and sys.argv[1] == '--host':
    print(json.dumps({}))
else:
    print("Requires an argument, please use --list or --host <host>")


============ +++++ ============

wget http://materials.example.com/dynamic/hosts -O inventory/hosts


[webservers]

[servers:children]
webservers

5. ansible -i inventory/inventorya.py webservers --list-hosts
6. 
ls -la inventory/inventorya.py 
chmod 755 inventory/inventorya.py
7.
chmod 755 inventory/inventoryw.py
8 
inventory/inventorya.py --list
9. inventory/inventoryw.py --list
10. cat inventory/hosts
11. ansible webservers --list-hosts
12. 

/home/student/dep-dynamic/inventory/hosts

add: 

[webservers]

[servers:children]
webservers

13. ansible webservers --list-hosts

LAB - DEPLOYING ANSIBLE
========================

[student@workstation -]$ lab deploy setup
1.1 yum list installed ansible
1.2 ansible --version
2.
[student@workstation -]$ mkdir /home/student/dep-lab
[student@workstation -]$ cd /home/student/dep-lab

3.[3.1 -3.3]

/home/student/dep-lab/ansible.cfg

[defaults]
remote_user = devops
inventory = inventory
[privilege_escalation]
become = False
becorne_method = sudo
become_user = root
become_ask_pass = False

4. /home/student/dep-lab/inventory

[student@workstation dep-lab]$ mkdir inventory
wget http://materials.example.com/dynamic/ inventory -0 inventory/inventory

wget http://materials.example.com/dynamic/ binventory.py -0 inventory/binventory.py
[student@workstation dep-lab]$ chmod 755 inventory/binventory.py

/home/student/dep-lab/inventory/inventory
[internetweb]
[intranetweb]
servera.lab.example.com
[everyone:children]
intranetweb
internetweb

5. 
ansible everyone -m command -a 'id'

6. ansible ever yone -m copy \ 
> -a 'cont ent="This server i s managed by Ansible.\n" dest=/etc/motd' - -become

7.
ansible ever yone -m copy \ 
> -a 'cont ent="This server i s managed by Ansible.\n" dest=/etc/motd' - -become
8. ansible everyone -m command -a 'cat /etc/motd'

9. lab deploy grade

+++++++++++++++++++++++++++++++++++++++++++++++
Guided Exercise: Writing and Running Play books
+++++++++++++++++++++++++++++++++++++++++++++++

/home/student/basic-playbook/site. yml

[student@workstation -]$ lab basic setup

~/basic-playbook/site.yml.

[1 -4 ]
[student@workstation -]$ cd -/basic-playbook


---
- name: Install and start Apache HTTPD
  hosts: web

  tasks:
    - name: httpd package is present
      yum:
        name: httpd
        state: present

    - name: correct index.html is present
      copy:
        src: files/index.html
        dest: /var/www/html/index.html

    - name: httpd is started
      service:
        name: httpd
        state: started
        enabled: true

5. 

ansible-playbook --syntax-check site.yml

6. ansible-playbook site.yml
7. ansible-playbook site.yml

=============================
Implementing Multiple Plays
=============================
[student@workstation -]$lab playbook setup

1.
cd /home/student/imp-playbook

[2-11]
/home/ student/imp-playbook/intranet.yml
---
- name: Enable intranet services
  hosts: servera.lab.example.com
  become: yes

  tasks:
    - name: Latest version of httpd and firewalld installed
      yum:
        name:
          - httpd
          - firewalld
        state: latest

    - name: firewalld enabled and running
      service:
        name: firewalld
        enabled: true
        state: started
    
    - name: firewalld permits http service
      firewalld:
        service: http
        permanent: true
        state: enabled
        immediate: yes

    - name: httpd enabled and running
      service:
        name: httpd
        enabled: true
        state: started

    - name: test html page is installed
      copy:
        content: "Welcome to the example.com intranet!\n"
        dest: /var/www/html/index.html

- name: Test intranet web server
  hosts: localhost
  become: no

  tasks:
    - name: connect to intranet web server
      uri:
        url: http://servera.lab.example.com
        status_code: 200


12    ansible-playbook --syntax-check intranet.yml

13. ansible-playbook intranet.yml
14. lab playbook grade


++++++++++++++++++++++++++++++++
Lab: Implementing Playbooks
++++++++++++++++++++++++++++++++
[student@workstation -]$ lab playbookinternet setup

cd /home/student/imp-lab

[2 -5 ]
vi /home/student/imp-lab/internet. yml

---
- name: Enable internet service
  hosts: serverb.lab.example.com
  become: yes

  tasks:
    - name: Check for the latest versions
      yum:
        name:
          - firewalld
          - httpd
          - php
          - php-mysql
          - mariadb-server
        state: latest

    - name: firewalld enabled and running
      service:
        name: firewalld
        enabled: true
        state: started

    - name: firewalld permits http service
      firewalld:
        service: http
        permanent: true
        state: enabled
        immediate: yes

    - name: httpd enabled and running
      service:
        name: httpd
        enabled: true
        state: started

    - name: mariadb enabled and running
      service:
        name: mariadb
        enabled: true
        state: started

    - name: html page is installed
      get_url:
        url: http://materials.example.com/grading/var/www/html/index.php
        dest: /var/www/html/index.php
        mode: 0644

- name: Test internet web server
  hosts: localhost
  become: no

  tasks:
    - name: connect to internet web server
      uri:
        url: http://serverb.lab.example.com
        status_code: 200


ansible-playbook --syntax-check internet.yml

6.    ansible-playbook internet.yml
7.    lab playbookinternet grade


+++++++++++++++++++++++++++++++++++++++++++++++
Managing Variables
+++++++++++++++++++++++++++++++++++++++++++++
[student@workstation -]$lab manage-variables-playbooks setup

1.
cd ~/dev-vars-playbook

[ 2 - 8]
vi playbook.yrnl

---
- name: Deploy and start Apache HTTPD service
  hosts: webserver
  vars:
    web_pkg: httpd
    firewall_pkg: firewalld
    web_service: httpd
    firewall_service: firewalld
    python_pkg: python-httplib2
    rule: http

  tasks:
    - name: Rquired packages are installed and up to date
      yum:
        name:
          - "{{ web_pkg }}"
          - "{{ firewall_pkg }}"
          - "{{ python_pkg }}"
        state: latest

    - name: The {{ firewall_service }} service is started and enabled
      service:
        name: "{{ firewall_service }}"
        enabled: true
        state: started

    - name: The {{ web_service }} service is started and enabled
      service:
        name: "{{ web_service }}"
        enabled: true
        state: started

    - name: Web content is in place
      copy:
        content: "Example web content"
        dest: /var/www/html/index.html

    - name: The firewall port for {{ rule }} is open
      firewalld:
        service: "{{ rule }}"
        permanent: true
        immediate: true
        state: enabled

- name: Verify the Apache service
  hosts: localhost
  become: false

  tasks:
    - name: Ensure the webserver is reachable
      uri:
        url: http://servera.lab.example.com
        status_code: 200

9.
ansible-playbook --syntax-check playbook.yml
10.
ansible-playbook playbook.yml

Cleanup
------------
[student@workstation -]$ lab manage-variables-playbooks cleanup


++++++++++++++++++++++
Managing Facts
++++++++++++++++++++++ 

lab manage-variables-facts setup

1.
cd ~/dev-vars-facts

2. 
ansible webserver -m setup
3. ansible webserver -m setup -a 'filter=ansible_user*'


4. custom.fact

[general]
package = httpd
service = httpd
state = started

5. 
setup_facts.yml

---
- name: Install remote facts
  hosts: webserver
  vars:
    remote_dir: /etc/ansible/facts.d
    facts_file: custom.fact
  tasks:
    - name: Create the remote directory
      file:
        state: directory
        recurse: yes
        path: "{{ remote_dir }}"
    - name: Install the new facts
      copy:
        src: "{{ facts_file }}"
        dest: "{{ remote_dir }}"

6. 
ansible webserver -m setup -a 'filter=ansible_local'		
		
7.
ansible-playbook --syntax-check setup_facts.yml

8. ansible-playbook setup_facts.yml

9. ansible webserver -m setup -a 'filter=ansible_local'

[10- 13]

playbook.yml

---
- name: Install Apache and start the service
  hosts: webserver

  tasks:
    - name: Install the required package
      yum:
        name: "{{ ansible_local['custom']['general']['package'] }}"
        state: latest
    - name: Start the service
      service:
        name: "{{ ansible_local['custom']['general']['service'] }}"
        state: "{{ ansible_local['custom']['general']['state'] }}"

14. ansible servera.lab.example.com -m command -a 'systemctl status httpd'

15. ansible-playbook --syntax-check playbook.yml

16. ansible-playbook playbook.yml
17. ansible servera.lab.example.com -m command -a 'systemctl status httpd'

Evaluation
lab manage-variables-facts grade

Cleanup

[student@workstation -]$ lab manage-variables-facts cleanup

+++++++++++++++++++++
Managing Inclusions
+++++++++++++++++++++

[student@workstation -]$ cd demo-vars-inclusions
2.
paths. yml

---
paths:
fileserver: /home/student/srv/filer/{{ ansible_fqdn }}
dbpath: /home/student/srv/database/{{ ansible_fqdn }}

3.
fileservers. yml
  ---
  - name: Configure fileservers
	hosts: fileservers
	tasks:
	name: Imports the variables file
	include_vars: paths.yml
	name: Creates the remote directory
	file:
	path: "{{ paths.fileserver }}"
	state: directory
	mode: 0755
	register: result
	name: Debugs the results
	debug:
	var: result
	
4.       ansible-playbook fileservers.yml

5. 
---
- name: Install the {{ package }} package
  hosts: dbservers
  tasks:
    - name: Imports the variables file
      include_vars: paths.yml
	  
    - name: Creates the remote directory
      file:
		path: "{{ paths.dbpath }}"
		state: directory
		mode: 0755

    - name: Debugs the results
      debug:
        var: result

		6.
student@workstationdemo-vars-inclusions]$ansible-playbookdbservers.yml

8.
install_package. yml

---
- name: Install the latest versions of httpd and mod_ssl
  yum:
  name: "{{ packages.web_pkg }}"
  state: latest

  9.
  
- name: Start the service
  service:
    name: "{{ web_service }}"
    state: started
---
- name: Install fileserver packages
hosts: fileservers
tasks:
name: Includes the variable
include_vars: package.yml
name: Installs the package
include: install_package.yml

10. inclusions]$ ansible-playbook playbook.yml

12. ansible-playbook playbook.yml


2.4

variables. yml
---
firewall_pkg: firewalld

[3.1 - 3.6 ]

---
- name: Configure web server
  hosts: webserver
  vars:
    rule: http

  tasks:
    - name: Include the variables from the YAML file
      include_vars: vars/variables.yml

    - name: Include the environment file and set the variables
      include: tasks/environment.yml
      vars:
        package: httpd
        service: httpd
        svc_state: started

    - name: Install the firewall
      yum:
        name: "{{ firewall_pkg }}"
        state: latest

    - name: Start the firewall
      service:
        name: firewalld
        state: started
        enabled: true

    - name: Open the port for {{ rule }}
      firewalld:
        service: "{{ rule }}"
        immediate: true
        permanent: true
        state: enabled

    - name: Create index.html
      copy:
        content: "{{ ansible_fqdn }} has been customized using Ansible on the {{ ansible_date_time['date'] }}\n"
        dest: /var/www/html/index.html

4. ansible-playbook --syntax-check playbook.yml

5. ansible-playbook playbook.yml

6. curl http://servera.lab.example.com

lab manage-variables-inclusions grade

lab manage-variables-inclusions cleanup

+++++++++++++++++++++++++++++++++++++++++
Lab: Managing Variables and Inclusions
++++++++++++++++++++++++++++++++++++++++

[student@workstation -]$ lab manage-variables setup

cd ~/lab-managing-vars

custom.fact

[packages]
db_package = mariadb-server
web_package = httpd

[services]
db_service = mariadb
web_service = httpd


setup_facts.yml
---
- name: Install the facts onto serverb
  hosts: lamp
  vars:
    remote_dir: /etc/ansible/facts.d
    facts_file: custom.fact
  tasks:
    - name: Create the remote directory
      file:
        state: directory
        recurse: yes
        path: "{{ remote_dir }}"
    - name: Install the new facts
      copy:
        src: "{{ facts_file }}"
        dest: "{{ remote_dir }}"
		
3.1 ansible-playbook --syntax-check setup_facts.yml
3.2 ansible-playbook setup_facts.yml
3.3 ansible lamp -m setup -a 'filter=ansible_local*'

4.1 [student@workstation lab-managing-vars]$ mkdir vars

main.yml
---
web_root: /var/www/html

5
[student@workstation lab-managing-vars]$ mkdir tasks
main.yml
---
  - name: Install the database and web server
    yum:
      name: 
        - "{{ ansible_local['custom']['packages']['db_package'] }}"
        - "{{ ansible_local['custom']['packages']['web_package'] }}"
      state: latest

  - name: Start the database service
    service:
      name: "{{ ansible_local['custom']['services']['db_service'] }}"
      state: started
      enabled: true

  - name: Start the web server service
    service:
      name: "{{ ansible_local['custom']['services']['web_service'] }}"
      state: started
      enabled: true
	  
6.
playbook.yml

---
- name: Install and configure lamp
  hosts: lamp
  vars:
    firewall: firewalld
  tasks:
  - name: Include the variable file
    include_vars: vars/main.yml
  - name: Include the tasks
    include: tasks/main.yml
  - name: Install the latest firewall version
    yum:
      name: "{{ firewall }}"
      state: latest
  - name: Start the firewall service
    service:
      name: "{{ firewall }}"
      state: started
      enabled: true
  - name: Set up port 80 to be listened on permanently
    firewalld:
      service: http
      state: enabled
      immediate: true
      permanent: true
  - name: Create index.html
    copy:
      content: "{{ ansible_fqdn }} ({{ ansible_default_ipv4['address'] }}) has been customized by Ansible\n"
      dest: "{{ web_root }}/index.html"

 6.6
	  [student@workstation lab-managing-vars]$ tree
7.	  
ansible-playbook --syntax-check playbook.yml

vars]$ ansible-playbook playbook.yml

[student@workstation lab-managing-vars]$ curl http://serverb

[student@workstation lab-managing-vars]$ ansible lamp -a 'systemctl status mariadb' 
[student@workstation -]$ lab manage-variables grade

lab manage-variables cleanup


+++++++++++++++++++++++++++++++
Guided Exercise: Constructing Flow Control
+++++++++++++++++++++++++++++++

[student@workstation -]$ lab task-control-flowcontrol setup
1.
cd ~/dev-flowcontrol
2.
configure_database.yml.

- yum:
    name: "{{ extra_packages }}"

- get_url:
    url: "http://materials.example.com/task_control/my.cnf"
    dest: "{{ configure_database_path }}"
    owner: mysql
    group: mysql
    mode: 0644
    seuser: system_u
    setype: mysqld_etc_t
    force: yes

- service:
    name: "{{ db_service }}"
    state: started
    enabled: true

	3-6
playbook.yml

---
- hosts: all
  vars:
    db_package: mariadb-server
    db_service: mariadb
    db_users:
      - db_admin
      - db_user
    configure_database_path: /etc/my.cnf
  tasks:
  - name: Create the MariaDB users
    user:
      name: "{{ item }}"
    with_items: "{{ db_users }}"
    when: inventory_hostname in groups['databases']
  - name: Install the database server
    yum:
      name: "{{ db_package }}"
    when: db_package is defined
  - name: Configure the database software
    include: configure_database.yml
    vars:
      extra_packages:
        - mariadb-bench
        - mariadb-libs
        - mariadb-test
    when: configure_database_path is defined

7.   ansible-playbook --syntax-check playbook.yml
8. 	ansible-playbook playbook.yml

9.1  ansible all -a 'yum list installed mariadb-bench mariadb-libs mariadb-test'
9.2  ansible all -a 'grep Ansible /etc/my.cnf'
9.3 ansible all -a 'id db_user'
9.4 ansible all -a 'id db_admin'
[student@workstation -]$lab task-control-flowcontrol grade
[student@workstation -]$lab task-control-flowcontrol cleanup

+++++++++++++++++++++++++++++++++++++++
Implementing Handlers
+++++++++++++++++++++++++++++++++++++++
[student@workstation -]$ lab task-control-handlers setup
1. [student@workstation -]$ cd ~/dev-handlers

2. [2.1 - 2.7]

configure_db.yml

---
- name: Installing Mariadb server
  hosts: databases
  vars:
    db_packages:
      - mariadb-server
      - MySQL-python
    db_service: mariadb
    src_file: "http://materials.example.com/task_control/my.cnf.template"
    dst_file: /etc/my.cnf
  tasks:
    - name: Install {{ db_packages }} package
      yum:
        name: "{{ item }}"
        state: latest
      with_items: "{{ db_packages }}"
      notify:
        - start_service
    - name: Download and install {{ dst_file }}
      get_url:
        url: "{{ src_file }}"
        dest: "{{ dst_file }}"
        owner: mysql
        group: mysql
        force: yes
      notify:
        - restart_service
        - set_password
    - name: Set the MySQL password
      mysql_user:
        name: root
        password: redhat
  handlers:
    - name: start_service
      service:
        name: "{{ db_service }}"
        state: started
    - name: restart_service
      service:
        name: "{{ db_service }}"
        state: restarted
    - name: set_password
      mysql_user:
        name: root
        password: redhat
		
3. student@workstationdev-handlers)$ansible-playbook--syntax-check configure_db.yml
4. ansible-playbook configure_db.yml
5. ansible-playbook configure_db.yml

7. [student@workstation dev-handlers]# ansible-playbook configure_db.yml
[student@workstation -]$lab task-control-handlers grade
[student@workstation -]$ lab task-control-handlers cleanup

+++++++++++++++++++++++++++++++++++++++
Implementing Tags
++++++++++++++++++++++++++++++++++++

[student@workstation -]$ lab task-control-tags setup

2
configure_mail.yml
---
- name: Install postfix
  yum:
    name: postfix
    state: latest
  tags:
    - server
  notify:
    - start_postfix
- name: Install dovecot
  yum:
    name: dovecot
    state: latest
  tags:
    - client
  notify:
    - start_dovecot
- name: Download main.cf configuration
  get_url:
    url: "http://materials.example.com/task_control/main.cf"
    dest: /etc/postfix/main.cf
  tags:
    - server
  notify:
    - restart_postfix

3
playbook.yml

---
- hosts: all
  tasks:
    - name: Include configure_mail.yml
      include:
        configure_mail.yml
      when: inventory_hostname in groups['mailservers']
  handlers:
    - name: start_postfix
      service:
        name: postfix
        state: started
    - name: start_dovecot
      service:
        name: dovecot
        state: started

4. ansible-playbook --syntax-check playbook.yml
5. ansible-playbook playbook.yml --tags 'server'
6. ansible mailservers -a 'yum list installed postfix'
7. ansible-playbook playbook.yml --skip-tags 'server'
8. ansible mailservers -a 'yum list installed dovecot'

lab task-control-tags grade
lab task-control-tags cleanup

++++++++++++++++++ +++++++++++++
Blocks also allow for error handling in combination with the rescue and always statements. If
any task in a block fails, tasks in its rescue block are executed in order to recover. After the
tasks in the block and possibly the rescue run, then tasks in its always block run. To summarize:
  - block: Defines the main tasks to run.
  - rescue: Defines the tasks that will be run if the tasks defined in the block clause fails.
  - always: Defines the tasks that will always run independently of the success or failure of tasks
defined in the block and rescue clauses.

The following example shows how to implement a block in a playbook. Even if tasks defined in the
block clause fail, tasks defined in the rescue and always clauses will be executed.


[student@workstation -]$lab task-control-failures setup
1. [student@workstation -]$ cd ~/dev-failures


---
- hosts: databases
  vars:
    web_package: httpd
    db_package: mariadb-server
    db_service: mariadb
  tasks:
	- name: Install {{ web_package }} package
	  yum:
	    name: "{{ web_package }}"
	    state: latest
	- name: Install {{ db_package }} package
	  yum:
	    name: "{{ db_package }}"
	    state: latest

4. [student@workstation dev-failures]$ ansible-playbook playbook.yml

5. 
---
- hosts: databases
  vars:
    web_package: httpd
    db_package: mariadb-server
    db_service: mariadb
  tasks:
	- name: Install {{ web_package }} package
	  yum:
	    name: "{{ web_package }}"
	    state: latest
	  ignore_errors: yes
	  
	- name: Install {{ db_package }} package
	  yum:
	    name: "{{ db_package }}"
	    state: latest

7.
		
playbook.yml

---
- hosts: databases
  vars:
    web_package: httpd
    db_package: mariadb-server
    db_service: mariadb

    - block:
        - name: Install {{ web_package }} package
          yum:
            name: "{{ web_package }}"
            state: latest
      rescue:
        - name: Install {{ db_package }} package
          yum:
            name: "{{ db_package }}"
            state: latest
      always:
        - name: Start {{ db_service }} service
          service:
            name: "{{ db_service }}"
            state: started

			

8. [student@workstation dev-failures]$ ansible-playbook playbook.yml

9. 
---
- hosts: databases
  vars:
    web_package: httpd
    db_package: mariadb-server
    db_service: mariadb
  tasks:
    - name: Check local time
      command: date
      register: command_result

    - name: Print local time
      debug:
        var: command_result['stdout']
    - block:
        - name: Install {{ web_package }} package
          yum:
            name: "{{ web_package }}"
            state: latest
      rescue:
        - name: Install {{ db_package }} package
          yum:
            name: "{{ db_package }}"
            state: latest
      always:
        - name: Start {{ db_service }} service
          service:
            name: "{{ db_service }}"
            state: started

[student@workstation dev-failures]$ ansible-playbook playbook.yml


9.3  
---
- hosts: databases
  vars:
    web_package: httpd
    db_package: mariadb-server
    db_service: mariadb
  tasks:
    - name: Check local time
      command: date
      register: command_result
      changed_when: false
    - name: Print local time
      debug:
        var: command_result['stdout']
    - block:
        - name: Install {{ web_package }} package
          yum:
            name: "{{ web_package }}"
            state: latest
      rescue:
        - name: Install {{ db_package }} package
          yum:
            name: "{{ db_package }}"
            state: latest
      always:
        - name: Start {{ db_service }} service
          service:
            name: "{{ db_service }}"
            state: started
			
9.4   [student@workstation dev-failures]$ ansible -playbook playbook.yml

10.1/2
  
---
- hosts: databases
  vars:
    web_package: httpd
    db_package: mariadb-server
    db_service: mariadb
  tasks:
    - name: Check local time
      command: date
      register: command_result
      changed_when: false
    - name: Print local time
      debug:
        var: command_result['stdout']
    - block:
        - name: Install {{ web_package }} package
          yum:
            name: "{{ web_package }}"
            state: latest
          failed_when: web_package == "httpd"
      rescue:
        - name: Install {{ db_package }} package
          yum:
            name: "{{ db_package }}"
            state: latest
      always:
        - name: Start {{ db_service }} service
          service:
            name: "{{ db_service }}"
            state: started
			
10.2    $ ansible-playbook playbook.yml

-]$lab task-control-failures cleanup

++++++++++++++++++++++++++++++
Implementing Task Control
+++++++++++++++++++++++++++++

(student@workstation -]$ lab task-control setup
cd ~/lab-task-control

install_packages.yml

---
- name: Install the latest versions of httpd and mod_ssl
  yum:
    name: "{{ item }}"
  with_items:
    - "{{ web_package }}"
    - "{{ ssl_package }}"
  when:
    - inventory_hostname in groups['webservers']
    - "(ansible_memory_mb['real']['total']) > (memory)"
- name: Start the service
  service:
    name: "{{ web_service }}"
    state: started

configure_firewall.yml

---
- yum:
    name: "{{ fw_package }}"
    state: latest
  tags: production
- service:
    name: "{{ fw_service }}"
    state: started
  tags: production
- firewalld:
    service: "{{ item }}"
    immediate: true
    permanent: true
    state: enabled
  with_items:
    - http
    - https
  tags: production


configure_web.yml

---
- shell:
    rpm -q httpd
  register: rpm_check
  failed_when: rpm_check.rc == 1
- block:
    - get_url:
        url: "{{ https_uri }}"
        dest: /etc/httpd/conf.d/
    - file:
        path: /etc/httpd/conf.d/ssl
        state: directory
        mode: 0755
    - file:
        path: /var/www/html/logs
        state: directory
        mode: 0755
    - stat:
        path: /etc/httpd/conf.d/ssl.conf
      register: ssl_file
    - shell:
        mv /etc/httpd/conf.d/ssl.conf /etc/httpd/conf.d/ssl.conf.bak
      when: ssl_file.stat.exists
    - unarchive:
        src: "{{ ssl_uri }}" 
        dest: /etc/httpd/conf.d/ssl/
        copy: false
      notify:
        - restart_services
    - copy:
        content: "{{ ansible_fqdn }} ({{ ansible_default_ipv4['address'] }}) has been customized by Ansible\n"
        dest: /var/www/html/index.html
  when: rpm_check.rc == 0



playbook.yml

---
- hosts: webservers
  tasks:
    - block:
      - include: install_packages.yml
        vars:
          memory: 256
          web_package: httpd
          ssl_package: mod_ssl
          web_service: httpd
      - include: configure_web.yml
        vars:
          https_uri: "http://materials.example.com/task_control/https.conf"
          ssl_uri: "http://materials.example.com/task_control/ssl.tar.gz"
      - include: configure_firewall.yml
        vars:
          fw_package: firewalld
          fw_service: firewalld
        tags: production
      rescue:
      - yum:
          name: httpd
          state: latest
        notify:
          - restart_services
      - debug:
          msg: "Failed to import and run all the tasks; installing the web server manually"
      always:
      - shell:
          cmd: "systemctl status httpd"
  handlers:
    - name: restart_services
      service:
        name: "{{ item }}"
        state: restarted
      with_items:
        - httpd
        - firewalld
6.3
student@workstationlab-task-control)$ansible-playbook --syntax -check playbook.yml

$ansible-playbook playbook.yml

curl -k https://serverb.lab.example.com

[student@workstation -]$ lab task-control grade

$ lab task-control cleanup


++++++++++++++++++++++++++++++++++
Implementing Jinja2  Templates
++++++++++++++++++++++++++++++++++

lab jinja2-implement setup 


cd ~/jinja2/

inventory

[webservers]
servera.lab.example.com

[workstations]
workstation.lab.example.com

motd.j2

This is the system {{ ansible_hostname }}.
Today's date is: {{ ansible_date_time['date'] }}.
Only use this sytem with permission.
You can ask {{ system_owner }} for access.


motd.yml

---
- hosts: all
  user: devops
  become: true
  vars:
    system_owner: clyde@example.com
  tasks:
    - template:
        src: motd.j2
        dest: /etc/motd
        owner: root
        group: root
        mode: 0644

5.  ansible-playbook --syntax-check motd.yml

6. ansible-playbook motd.yml
ssh devops@servera.lab.example.com
lab jinja2-implement grade
lab jinja2-implement cleanup


lab jinja2-lab setup 

ansible.cfg
[defaults]
inventory = inventory
ansible_managed = Ansible managed: modified on %Y-%m-%d %H:%M:%S


inventory

[servers]
serverb.lab.example.com

motd.j2

The system's total memory is: {{ ansible_memfree_mb }} MBs.
The current free memory is: {{ ansible_memtotal_mb }} MBs.

motd.yml

---
- hosts: servers
  user: devops
  become: yes
  tasks:
    - template:
        src: motd.j2
        dest: /etc/motd
        owner: root
        group: root
        mode: 0644

		

5.  ansible-playbook --syntax-check motd.yml

6. ansible-playbook motd.yml


lab jinja2-lab grade
lab jinja2-lab cleanup

+++++++++++++++++++++++++++++++++
 ROLE
+++++++++++++++++++++++++++++++++

The files subdirectory contains fi xed-content files and the templates subdirectory contains
templates that can be deployed by the role when it is used.

A good place to start would be the ROLENAME/tasks/main.yml file. This file defines which modules to
call on the managed hosts that this role is applied.

lab creating-roles setup

cd ~/dev-roles

3.
[student@workstation dev-roles]$ mkdir -p roles/myvhost/{files,handlers}
[student@workstation dev-roles]$ mkdir roles/myvhost/{meta,tasks,templates}
3.

install-roles.yml
---
# install-roles.yml

- src: http://materials.example.com/roles-library/student.bash_env.tgz
  name: student.bash_env
[student@workstation dev-roles]$ ls roles/

dev-roles ]$ ansible-galaxy install -p roles -r installroles.yml


use-bash_env-role.yml

---
- name: use student.bash_env role playbook
  hosts: webservers
  user: devops
  become: true

  roles:
    - student.bash_env

	student@workstationdev-roles)$ansible-playbook -iinventory use-bash_env-role.yml
md5sum roles/student.bash_env/templates/*

ssh servera md5sum /etc/skel/{.bash_profile, .bashrc, .vimrc}

ansible-galaxy init --offline -p roles student.myenv



roles/student.myenv/tasks/main.yml

---
# tasks file for student.myenv

- name: check myenv_user default
  fail:
    msg: "You must specify the variable 'myenv_user' to use this role!"
  when: "myenv_user == ''"

- name: install my packages
  yum:
      name: "{{ item }}"
      state: installed
  with_items: "{{ myenv_packages }}"

- name: copy the profile picture
  copy:
    src: profile.png
    dest: "~{{ myenv_user }}/profile.png"

- name: adding to the .bashrc
  lineinfile:
   line: "alias tree='tree -C'"
   path: "~{{ myenv_user }}/.bashrc"

- name: template out mkcd function
  template:
    src: mkcd.sh.j2
    dest: /etc/profile.d/mkcd.sh
    owner: root
    group: root
    mode: 0644

roles/student. myenv/vars/main. yml
---
# vars file for student.myenv

myenv_packages:
  - 'git'
  - 'tree'
  - 'vim-enhanced'

roles/student. myenv/defaults/main. yml

---
# defaults file for student.myenv

myenv_user: ''

8.

myenv.yml
8.1
---
- name: setup my personal environment
  hosts: all
  roles:
    - role: student.myenv
8.2
ansible-playbook myenv.yml

9.
---
- name: setup my personal environment
  hosts: all
  roles:
    - role: student.myenv
      myenv_user: student
	  
9.2
ansible-playbook myenv.yml

10. [student@workstation lab-roles]$ mv apache_*.j2 roles/myapache/templates
	  [student@workstation lab-roles]$ ls roles/myapache/templates

11. roles/myapache/handlers/main.yml

---
# handlers file for myapache

- name: restart apache
  service:
    name: httpd
    state: restarted
	
roles/myapache/tasks/main.yml

---
# tasks file for myapache

- name: install the httpd and firewalld packages
  yum:
    name: "{{ item }}"
    state: latest
  with_items:
    - httpd
    - firewalld

- name: copy the apache_httpdconf.j2 template
  copy:
    src: templates/apache_httpdconf.j2
    dest: /etc/httpd/conf/httpd.conf
    owner: root
    group: root
    mode: 0444
  notify:
    - restart apache
  when: apache_enable

- name: copy the apache_indexhtml.j2 template
  copy:
    src: templates/apache_indexhtml.j2
    dest: /var/www/html/index.html
    owner: root
    group: root
    mode: 0444
  when: apache_enable

- name: start and enable the httpd and firewalld services
  service:
    name: "{{ item }}"
    state: restarted
  with_items:
    - httpd
    - firewalld
  when: apache_enable

- name: start up port 80
  firewalld:
    port: 80/tcp
    immediate: true
    permanent: true
    state: enabled
  when: apache_enable


13.
roles/myapache/defaults/main.yml

---
# defaults file for myapache

apache_enable: false

apache.yml

---
- name: set up apache on serverb
  hosts: serverb.lab.example.com
  roles:
    - role: myapache

12.4

[student@workstation lab-roles]$ ansible-playbook apache.yml	  


15.

apache.yml

---
- name: set up apache on serverb
  hosts: serverb.lab.example.com
  roles:
    - role: myapache
      apache_enable: true

[student@workstation lab-roles]$ ansible-playbook apache.yml
[student@workstation lab-roles]$ curl -s http://serverb.lab.example.com
-]$ lab ansible-roles-lab grade
$ lab ansible-roles-lab cleanup


++++++++++++++++++++++++++++++

HOST PATTERN

++++++++++++++++++++++

srv1.example.com
srv2.example.com
s1.lab.example.com
s2.lab.example.com

[web]
jupiter.lab.example.com
saturn.example.com

[db]
db1.example.com
db2.example.com
db3.example.com

[lb]
lb1.lab.example.com
lb2.lab.example.com

[boston]
db1.example.com
jupiter.lab.example.com
lb2.lab.example.com

[london]
db2.example.com
db3.example.com
file1.lab.example.com
lb1.lab.example.com

[dev]
web1.lab.example.com
db3.example.com

[stage]
file2.example.com
db2.example.com

[prod]
lb2.lab.example.com
db1.example.com
jupiter.lab.example.com

[function:children]
web
db
lb
city

[city:children]
boston
london
environments

[environments:children]
dev
stage
prod
new

[new]
172.25.252.23
172.25.252.44
172.25.252.32



[student@workstation -]$ lab patterns setup

cd /home/student/patterns


ansible dbl.example.com -i inventory --list-hosts

ansible 172.25.252.44 -i inventory --list-hosts

ansible all -i inventory --list-hosts

ansible london -i inventory --list-hosts

ansible environments -i inventory --list-hosts

ansible ungrouped -i inventory --list-hosts

ansible '*.example.com' -i inventory --list-hosts

ansible '*.example.com, !*.lab.example.com' \
> -i inventory --list-hosts

ansible lbl.lab.example.com,\
> s1.lab.example.com,db1.example.com -i inventory --list-hosts

ansible '172.25.*' -i inventory --list-hosts

ansible 's*' -i inventory --list-hosts

ansible 'prod,172*,*lab*' -i inventory --list-hosts

ansible 'db,&london' -i inventory --list-hosts

++++++++++++++++++++++++++++++++++++++++++++
Configuring Delegation
++++++++++++++++++++++++++++++++++++++++

[student@workstation -]$ lab configure-delegation setup

[student@workstation -]$ cd ~/configure-delegation

~/configure-delegation/inventory/hosts

[webservers]
servera.lab.example.com

[proxyservers]
serverc.lab.example.com

[student@workstation configure-delegation]$ mv servera.lab.example.com-httpd.conf.j2 ~/configure-delegation/templates

[student@workstation configure-delegation]$ mv serverc.lab.example.com-httpd.conf.j2 ~/configure-delegation/templates

configure-delegation\templates

index.html.j2
The webroot is {{ ansible_fqdn }}

site.yml
---
- name: Install and configure httpd
  hosts: all
  remote_user: devops
  become: true

  tasks:
    - name: Install httpd
      yum:
        name: httpd
        state: latest
    - name: Start and enable httpd
      service:
        name: httpd
        state: started
        enabled: true
    - name: Install firewalld
      yum:
        name: firewalld
        state: latest
    - name: Start and enable firewalld
      service:
        name: firewalld
        state: started
        enabled: true
    - name: Enable the firewall
      firewalld:
        zone: public
        service: http
        permanent: true
        state: enabled
        immediate: true
    - name: template server configs
      template:
        src: "templates/{{ inventory_hostname }}-httpd.conf.j2"
        dest: /etc/httpd/conf.d/myconfig.conf
        owner: root
        group: root
        mode: 0644
      notify:
        - restart httpd
  handlers:
    - name: restart httpd
      service:
        name: httpd
        state: restarted
- name: Deploy web service and disable proxy server
  hosts: webservers
  remote_user: devops
  become: true
  tasks:
    - name: Stop Apache proxy server
      service:
        name: httpd
        state: stopped
      delegate_to: "{{ item }}"
      with_items: "{{ groups['proxyservers'] }}"
    - name: Deploy webpages
      template:
        src: templates/index.html.j2
        dest: /var/www/html/index.html
        owner: apache
        group: apache
        mode: 0644
    - name: Start Apache proxy server
      service:
        name: httpd
        state: started
      delegate_to: "{{ item }}"
      with_items: "{{ groups['proxyservers'] }}"

ansible-playbook --syntax-check site.yml

ansible-playbook site.yml

curl http://serverc.lab.example.com/ external

[student@workstation -]$ lab configure-delegation cleanup

+++++++++++++++++++++++++++++++++++++++++
Configuring Parallelism
++++++++++++++++++++++++++++++++++

lab configure-async setup

cd ~/configure-async

~/configure-async/templates/longfiles.j2

#!/usr/bin/bash
echo "emptying $2" > $2
for i in {00..30}; do
  echo "run $i, $1"
  echo "run $i for $1" >> $2
  sleep 1
done


---
# async.yml

- name: longfiles async playbook
  hosts: webservers
  remote_user: devops
  become: true
  tasks:
    - name: template longfiles script
      template:
        src: templates/longfiles.j2
        dest: /usr/local/bin/longfiles
        owner: root
        group: root
        mode: 0755
    - name: run longfiles script
      command: "/usr/local/bin/longfiles {{ item }} /tmp/{{ item }}.file"
      async: 110
      poll: 0
      with_items:
        - foo
        - bar
        - baz
      register: script_sleeper
    - name: show script_sleeper value
      debug:
        var: script_sleeper
    - name: check status of longfiles script
      async_status: "jid={{ item['ansible_job_id'] }}"
      register: job_result
      until: job_result['finished']
      retries: 30
      with_items: "{{ script_sleeper['results'] }}"

[student@workstation configure-async]$ ansible-playbook --syntax-check async.yml

[student@workstation configure-async]$ ansible-playbook async.yml

[student@workstation -]$ lab configure-async cleanup

+++++++++++++++++++++++++
 Optimizing Ansible
 ++++++++++++++++++++++++
 
 lab optimize-ansible-lab setup
 
cd ~/lab-optimizing-ansible

curl http://serverd.lab.example.coml


lab-optimizing-ansible\templates

<html>
<head><title>My Page</title></head>
<body>
<h1>
Welcome to {{ inventory_hostname }}.
</h1>
<h2>A new feature added.</h2>
</body>
</html>


  ~/lab-optimizingansible-ansible
upgrade_webserver.yml

---
- name: Upgrade web server
  hosts: webservers
  remote_user: devops
  become: true
  serial: 1
  tasks:
    - name: Remove the web server from the load balancer pool
      haproxy:
        state: disabled
        backend: app
        host: "{{ inventory_hostname }}"
        socket: /var/lib/haproxy/stats
        wait: true
      delegate_to: "{{ item }}"
      with_items: "{{ groups['lbserver'] }}"
    - name: Copy the template
      template:
        src: templates/index-ver1.html.j2
        dest: /var/www/html/index.html
      register: pageupgrade
    - name: Restart the web servers
      command: shutdown -r +1 "Ansible updates triggered"
      async: 1
      poll: 0
      ignore_errors: true
      when: pageupgrade['changed']
    - name: Wait for the web server to reboot
      wait_for:
        host: "{{ inventory_hostname }}"
        state: started
        delay: 80
        timeout: 200
      become: false
      delegate_to: localhost
      when: pageupgrade['changed']
    - name: Wait for port to open up to be listened on
      wait_for:
        host: "{{ inventory_hostname }}"
        state: started
        port: 80
        timeout: 20
    - name: Add the web server to the load balancer pool
      haproxy:
        state: enabled
        backend: app
        host: "{{ inventory_hostname }}"
        socket: /var/lib/haproxy/stats
        wait: true
      delegate_to: "{{ item }}"
      with_items: "{{ groups['lbserver'] }}"

$ ansible-playbook --syntax-check upgrade_webserver.yml

ansible-playbook upgrade_webserver.yml

curl http://serverd.lab.example.com

curl http://serverd.lab.example.com





[student@workstation -]$ lab optimize-ansible-lab grade

[student@workstation -]$ lab optimize-ansible-lab cleanup

+++++++++++++++++++++++++++++++++++++
 lab execute-ansible-vault setup
+++++++++++++++++++++++++++++++++++++

 lab execute-ansible-vault setup
 
 cd ~/execute-ansible-vault
 
ansible-galaxy init --offline -p roles/ 
encryptdisk

ansible-vault encrypt roles/encryptdisk/vars/main.yml

secret.yml

roles\encryptdisk\vars

$ANSIBLE_VAULT;1.1;AES256
35386165383435356234323833616434353166333438656463663663633632363637363130376234
6464666138333464633961356234616138643161396562620a333566653831623462356239653137
66343039313362323362616661343438613439373436653865343338353662373966303731336439
6130373538386163380a636337393064326339306534613961393062303837353536383766333337
36306562343438663536656130313364343839323166346161363335613833623235326435663533
62663333643936646538373966306332653661326163386134643036356364643131623631316436
63353035303037643362366538383663633231356331343765313265626133653963656631343234
62376431346534376563353632336638643362613631663530313132616566646639616236633232
6164

create_users.yml
---
- name: create user accounts based on the encrypted file
  hosts: devservers
  become: true
  remote_user: devops
  vars_files:
    - secret.yml
  tasks:
    - name: creating users from secret.yml
      user:
        name: "{{ item['name'] }}"
        password: "{{ item['pw'] | password_hash('sha512') }}"
      with_items: "{{ newusers }}"

$ ansible-playbook --syntax-check --ask vault-pass create_users.yml

ansible-playbook --vault-password -file=vault-pass create_users.yml

[student@workstation -]$ lab execute-ansible-vault grade

[student@workstation -]$ lab execute-ansible-vault cleanup

7.ansible-playbook --ask-vault-pass encrypt.yml
8. ansible prodservers -a 'lsblk'

9. wget http://materials.example.com/ playbooks/keyfile-encrypted.j2

10. ansible-vault decrypt keyfile-encrypted.j2 --output=roles/encryptdisk/templates/keyfile.j2

11. ansible-vault edit roles/encryptdisk/vars/main.yml
12. roles\encryptdisk\defaults

main.yml
---
# defaults file for encryptdisk

addkey: no

13. roles\encryptdisk\tasks

roles/encryptdisk/tasks/main.yml

---
# tasks file for chapter9-lab
  - name: Check if device already unlocked.
    ignore_errors: True
    shell: stat /dev/mapper/{{ luks_name }} > /dev/null; echo $?;
    register: already_unlocked

  - name: Umount volumes
    shell: umount /dev/mapper/{{ luks_name }} || true
    when: already_unlocked.stdout == "0"

  - name: Close disk... because of crypting/action requested
    command: cryptsetup luksClose /dev/mapper/{{ luks_name }}
    when: already_unlocked.stdout == "0"

  - name: Check if device already unlocked.
    check_mode: no
    shell: stat /dev/mapper/{{ luks_name }} > /dev/null; echo $?;
    register: already_unlocked

  - name: encrypt disk
    shell: printf {{ luks_pass }} | cryptsetup --verbose luksFormat {{ luks_dev }}
          
  - name: open encrypted disk
    shell: printf {{ luks_pass }} | cryptsetup luksOpen {{ luks_dev }} {{ luks_name }}
    when: already_unlocked.stdout != "0"
  
  - name: Create directory /{{ luks_name }}
    file: path=/{{ luks_name }} state=directory
        
  - name: mkfs on {{ luks_dev }}
    command: mkfs.xfs /dev/mapper/{{ luks_name }} -f
  
  - name: create cryptab file
    crypttab: name={{ luks_name }} backing_device={{ luks_dev }} state=present

  - name: mount the /dev/mapper/{{ luks_name }}
    mount: name=/{{ luks_name }} src=/dev/mapper/{{ luks_name }} fstype=xfs state=mounted

  - name: get the keyfile for LUKS key
    template:
      src: "{{ luks_key }}"
      dest: /root/keyfile
      owner: root
      group: root
      mode: 0600
    when: addkey

  - name: add new keyslot to encrypted disk
    shell: echo {{ luks_pass }} | cryptsetup luksAddKey {{ luks_dev }} /root/keyfile
    when: addkey


14. echo password I cryptsetup luksAddKey devicename /pathlto/keyfilename

~/lab-ansible-vault/encrypt. yml
---
- name: Encrypt disk on serverb using LUKS
  hosts: prodservers
  remote_user: devops
  become: true
  roles:
    - role: encryptdisk
      addkey: true

ansible-playbook --syntax-check --askvault-pass encrypt.yml

vault]$ ansible-playbook --ask-vault-pass encrypt.yml

ssh root@serverb
cryptsetup luksDump /dev/vdb
umount /crypto
cryptsetup close crypto
cryptsetup open /dev/vdb crypto -d /root/keyfile
]# mount -a
-]# lsblk
#exit
lab ansible-vault-lab grade
lab ansible-vault-lab cleanup


++++++++++++++++++++++++++++ 
Troubleshoot
++++++++++++++++++++++++

 lab troubleshoot-playbook setup
 
 cd ~/troubleshooting
 
ansible.cfg 

[defaults]
inventory   = /home/student/troubleshooting/inventory
remote_user = devops
log_path    = /home/student/troubleshooting/ansible.log

[privilege_escalation]
become      = true

[student@workstation troubleshooting]$ ansible-playbook samba.yml

---
- name: Install a samba server
  hosts: samba_servers
  user: devops
  become: true
  vars:
    install_state: installed
    random_var: "This is colon: test"

  tasks:
  - name: install samba
    yum: 
      name: samba
      state: "{{ install_state }}"

  - name: install firewalld
    yum:
      name: firewalld
      state: installed
  
  - name: debug install_state variable
    debug: msg="The state for the samba service is {{ install_state }}"

  - name: start samba
    service:
      name: smb
      state: started
      enabled: yes

  - name: start firewalld
    service:
      name: firewalld
      state: started
      enabled: yes

  - name: configure firewall for samba
    firewalld:
      state: enabled
      permanent: true
      immediate: true
      service: samba

  - name: deliver samba config
    template:
      src: samba.conf.j2
      dest: /etc/samba/smb.conf
      owner: root
      group: root
      mode: 0644

ansible-playbook samba.yml --syntax-check

12. ping -c3 servera.lab.example.com

ssh devops@servera.lab.example.com

ansible-playbook -vvvv samba.yml

17.
/home/student/troubleshooting/

samba.j2
# {{ random_var }}
    [global]
      workgroup = KAMANSI
      server string = Samba Server Version %v
      log file = /var/log/samba/log.%m
      max log size = 50
      security = user
      passdb backend = tdbsam
      load printers = yes
      cups options = raw
    [homes]
      comment = Home Directories
      browseable = no
      writable = yes
    [printers]
      comment = All Printers
      path = /var/spool/samba
      browseable = no
      guest ok = no
      writable = no
      printable = yes
	  

19. ansible-playbook samba.yml --step

ansible-playbook --check playbook.yml

-]# ansible-playbook --check --diff playbook.yml

Ansible also provides the - -di ff option. This option reports the changes done to the template
files on managed hosts. If used with the - - check option, those changes are displayed and not
actually made.


ansible demohost -u devops -b -m yum -a 'name=httpd state=present'

ansible demohost -a 'lsblk'

ansible demohost -a 'free -m'

++++++++++++++++++++++++++++++++++++++
troubleshoot-managedhosts
+++++++++++++++++++++++++++++++++++++++


lab troubleshoot-managedhosts setup

cd ~/troubleshooting


---
# start of mailrelay playbook
- name: create mail relay servers
  hosts: mailrelay
  user: devops
  become: true

  tasks:
    - name: install postfix package
      yum:
        name: postfix
        state: installed

    - name: install mail config files
      template:
        src: postfix-relay-main.conf.j2
        dest: /etc/postfix/main.cf
        owner: root
        group: root
        mode: 0644
      notify: restart postfix

    - name: check main.cf file
      stat: path=/etc/postfix/main.cf
      register: maincf

    - name: verify main.cf file exists
      debug: msg="The main.cf file exists"
      when: maincf.stat.exists is defined

    - name: start and enable mail services
      service:
        name: postfix
        state: started
        enabled: yes

    - name: check for always_bcc
      command: /usr/sbin/postconf always_bcc
      register: bcc_state
      ignore_errors: true

    - name: email notification of always_bcc config
      mail:
        to: student@serverb.example.com
        subject: 'always_bcc setting is not empty'
        body: "always_bcc is {{bcc_state.stdout}}"
      when: bcc_state.stdout != 'always_bcc ='

    - name: postfix firewalld config
      firewalld:
        state: enabled
        permanent: true
        immediate: true
        service: smtp

  handlers:
    - name: restart postfix
      service:
        name: postfix
        state: restarted

# end of mailrelay play

ansible-playbook mailrelay.yml --check


postfix-relay-main.conf.j2

ansible servera.lab.example.com -u devops -b -a "head /etc/postfix/main.cf"

4. ansible-playbook mailrelay.yml

ansible servera.lab.example.com -u devops -b -a "head /etc/postfix/main.cf"

9. telnet servera.lab.example.com 25


++++++++++++++++++++++
Troubleshooting Ansible
++++++++++++++++++++++++++++

lab troubleshoot-lab setup

ansible.cfg
[defaults]
inventory = /home/student/troubleshooting-lab/inventory-lab

inventory-lab

[webservers]
serverb.lab.example.com

vhosts.conf

<VirtualHost serverb.lab.example.com>
    ServerAdmin webmaster@foob.example.com
    ServerName serverb.lab.example.com
    ErrorLog logs/serverb-ssl.error.log
    CustomLog logs/serverb-secure.common.log common
    DocumentRoot /var/www/vhosts/serverb-secure/

    SSLEngine On
    SSLCertificateFile /etc/pki/tls/certs/serverb.lab.example.com.crt
    SSLCertificateKeyFile /etc/pki/tls/private/serverb.lab.example.com.key

    <Directory /var/www/vhosts/serverb-secure>
        Options +Indexes +followsymlinks +includes
        Order allow,deny
        allow from all
    </Directory>
</VirtualHost>


1.2 [ failed ]

vars:
  random_var: This is colon: test

1.3:

... output omitted ...
vars:
random_var: "This is colon: test"

2.1  - name: start and enable web services

extra space at the beggining of the start 

2.2 fixed that and run 

3.1  yum:
name: {{ item }}
" here

3.2 
Correct the item variable in the install web server packages task. Add double
quotes to { { item }}

name: "{{ item }}"

3.3 ansible-playbook secure-web.yml -syntax-check

secure-web.yml

4.1 ansible-playbook secure-web.yml
4.2 ansible-playbook secure-web.yml -vvvv

7.2 lab]$ ansible all -u devops -b -a 'systemctl status httpd'
7.4 ansible all -u devops -b -a 'systemctl status httpd'

---
# start of secure web server playbook
- name: create secure web service
  hosts: webservers
  user: devops
  become: true
  vars:
    random_var: "This is colon: test"

  tasks:
    - block:
        - name: install web server packages
          yum:
            name: "{{ item }}"
            state: latest
          notify:
            - restart services
          tags:
            - packages
          with_items:
            - httpd
            - mod_ssl
            - crypto-utils

        - name: install httpd config files
          copy:
            src: vhosts.conf
            dest: /etc/httpd/conf.d/vhosts.conf
            backup: yes
            owner: root
            group: root
            mode: 0644
          register: vhosts_config
          notify:
            - restart services
          tags:
            - config

        - name: create ssl certificate
          command: openssl req -new -nodes -x509 -subj "/C=US/ST=North Carolina/L=Raleigh/O=Example Inc/CN=serverb.lab.example.com" -days 120 -keyout /etc/pki/tls/private/serverb.lab.example.com.key -out /etc/pki/tls/certs/serverb.lab.example.com.crt -extensions v3_ca
          args:
            creates: /etc/pki/tls/certs/serverb.lab.example.com.crt

        - name: start and enable web services
          service:
            name: httpd
            state: started
            enabled: yes
          tags:
            - services

        - name: deliver content
          copy:
            dest: /var/www/vhosts/serverb-secure
            src: html/

        - name: check httpd syntax
          command: /sbin/httpd -t
          register: httpd_conf_syntax
          failed_when: "'Syntax OK' not in httpd_conf_syntax.stderr"

        - name: httpd_conf_syntax variable
          debug: msg="The httpd_conf_syntax variable value is {{ httpd_conf_syntax }}"

        - name: check httpd status
          command: /sbin/service httpd status
          register: httpd_status_output
          failed_when: "'Active: failed' in httpd_status_output.stdout"
          changed_when: "'Active: inactive' in httpd_status_output.stdout"
          notify:
            - restart services

      rescue:
        - name: recover original httpd config
          file:
            path: /etc/httpd/conf.d/vhosts.conf
            state: absent
          notify:

        - name: email notification of httpd config status
          mail:
            to: student@serverb.lab.example.com
            subject: 'httpd config is not correct'
            body: "httpd syntax is {{httpd_conf_syntax.stdout}}"
          when: httpd_conf_syntax.stdout != 'Syntax OK'
           - restart services

  handlers:
    - name: restart services
      service:
        name: httpd
        state: restarted

# end of secure web play

lab troubleshoot-lab grade

lab troubleshoot-lab cleanup

