# ansible-team1
team1 playbook for ansible advanced training

## Prepare to run 

### 1. install [geerlingguy.postgresql](https://galaxy.ansible.com/geerlingguy/postgresql) role 


`ansible-galaxy install geerlingguy.postgresql`


```text
$ ansible-galaxy install geerlingguy.postgresql
- downloading role 'postgresql', owned by geerlingguy
- downloading role from https://github.com/geerlingguy/ansible-role-postgresql/archive/1.3.1.tar.gz
- extracting geerlingguy.postgresql to /Users/jianfeng/.ansible/roles/geerlingguy.postgresql
- geerlingguy.postgresql (1.3.1) was installed successfully
```

### 2. edit inventory

Please note ansible variable `GUID="abcd"` is added. And you must replace `abcd` with your own GUID on [opentlc](https://labs.opentlc.com) environment.

```ini
[3tierapp:vars]

###########################################################################
### Ansible Vars
###########################################################################
timeout=60
ansible_become=yes
ansible_ssh_user=ec2-user
ansible_ssh_private_key_file="~/.ssh/bcfckey.pem"
ansible_ssh_common_args="-o StrictHostKeyChecking=no"

GUID="bcfc"

[3tierapp:children]
frontends
apps
appdbs
support


[frontends]
## These are the frontends
frontend1.bcfc.internal ansible_ssh_host=frontend1.bcfc.example.opentlc.com

[apps]
## These are the apps
app1.bcfc.internal ansible_ssh_host=app1.bcfc.example.opentlc.com
app2.bcfc.internal ansible_ssh_host=app2.bcfc.example.opentlc.com

[appdbs]
## These are the appdbs
appdb1.bcfc.internal ansible_ssh_host=appdb1.bcfc.example.opentlc.com

## These are the support
[support]
support1.bcfc.internal ansible_ssh_host=support1.bcfc.example.opentlc.com
```

## Run playbook

```sh
$ git clone 
$ cd ansible-team1/3tier-good
$ ansible-playbook -i <YOUR_INVENTORY_FILE> main.yml
```

Execute result would looks like below.
```text
$ ansible-playbook -i ../../myinventory.ini main.yml
PLAY [configuration] *****************************************************************************************************

TASK [enable repos] ******************************************************************************************************
changed: [support1.bcfc.internal]
changed: [frontend1.bcfc.internal]
changed: [app1.bcfc.internal]
changed: [appdb1.bcfc.internal]
changed: [app2.bcfc.internal]

PLAY [deploy haproxy] ****************************************************************************************************

TASK [http] **************************************************************************************************************
changed: [frontend1.bcfc.internal]

TASK [install HAProxy] ***************************************************************************************************
changed: [frontend1.bcfc.internal]

TASK [enable HAProxy] ****************************************************************************************************
changed: [frontend1.bcfc.internal]

TASK [configure haproxy] *************************************************************************************************
changed: [frontend1.bcfc.internal]

RUNNING HANDLER [restart HAProxy] ****************************************************************************************
changed: [frontend1.bcfc.internal]

PLAY [deploy tomcat] *****************************************************************************************************

TASK [install tomcat] ****************************************************************************************************
changed: [app1.bcfc.internal]
changed: [app2.bcfc.internal]

TASK [enable tomcat at boot] *********************************************************************************************
changed: [app2.bcfc.internal]
changed: [app1.bcfc.internal]

TASK [create ansible tomcat directory] ***********************************************************************************
ok: [app2.bcfc.internal]
ok: [app1.bcfc.internal]

TASK [copy static index.html to tomcat webapps/ansible/index.html] *******************************************************
ok: [app1.bcfc.internal]
ok: [app2.bcfc.internal]

TASK [start tomcat] ******************************************************************************************************
changed: [app1.bcfc.internal]
changed: [app2.bcfc.internal]

PLAY [deploy postgres] ***************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************
ok: [appdb1.bcfc.internal]

TASK [geerlingguy.postgresql : include] **********************************************************************************
included: /Users/jianfeng/.ansible/roles/geerlingguy.postgresql/tasks/variables.yml for appdb1.bcfc.internal

TASK [geerlingguy.postgresql : Include OS-specific variables (Debian).] **************************************************
skipping: [appdb1.bcfc.internal]

TASK [geerlingguy.postgresql : Include OS-specific variables (RedHat).] **************************************************
ok: [appdb1.bcfc.internal]

TASK [geerlingguy.postgresql : Define postgresql_packages.] **************************************************************
ok: [appdb1.bcfc.internal]

TASK [geerlingguy.postgresql : Define postgresql_daemon.] ****************************************************************
ok: [appdb1.bcfc.internal]

TASK [geerlingguy.postgresql : Define postgresql_version.] ***************************************************************
ok: [appdb1.bcfc.internal]

TASK [geerlingguy.postgresql : Define postgresql_data_dir.] **************************************************************
ok: [appdb1.bcfc.internal]

TASK [geerlingguy.postgresql : Define postgresql_bin_path.] **************************************************************
ok: [appdb1.bcfc.internal]

TASK [geerlingguy.postgresql : Define postgresql_config_path.] ***********************************************************
ok: [appdb1.bcfc.internal]

TASK [geerlingguy.postgresql : include] **********************************************************************************
included: /Users/jianfeng/.ansible/roles/geerlingguy.postgresql/tasks/setup-RedHat.yml for appdb1.bcfc.internal

TASK [geerlingguy.postgresql : Ensure PostgreSQL packages are installed.] ************************************************
ok: [appdb1.bcfc.internal] => (item=postgresql)
changed: [appdb1.bcfc.internal] => (item=postgresql-server)
ok: [appdb1.bcfc.internal] => (item=postgresql-contrib)
ok: [appdb1.bcfc.internal] => (item=postgresql-libs)

TASK [geerlingguy.postgresql : Ensure PostgreSQL Python libraries are installed.] ****************************************
ok: [appdb1.bcfc.internal]

TASK [geerlingguy.postgresql : include] **********************************************************************************
skipping: [appdb1.bcfc.internal]

TASK [geerlingguy.postgresql : Set PostgreSQL environment variables.] ****************************************************
ok: [appdb1.bcfc.internal]

TASK [geerlingguy.postgresql : Ensure PostgreSQL data directory exists.] *************************************************
ok: [appdb1.bcfc.internal]

TASK [geerlingguy.postgresql : Check if PostgreSQL database is initialized.] *********************************************
ok: [appdb1.bcfc.internal]

TASK [geerlingguy.postgresql : Ensure PostgreSQL database is initialized.] ***********************************************
skipping: [appdb1.bcfc.internal]

TASK [geerlingguy.postgresql : Configure global settings.] ***************************************************************
ok: [appdb1.bcfc.internal] => (item={u'option': u'unix_socket_directories', u'value': u'/var/run/postgresql'})

TASK [geerlingguy.postgresql : Configure host based authentication (if entries are configured).] *************************
ok: [appdb1.bcfc.internal]

TASK [geerlingguy.postgresql : Ensure PostgreSQL unix socket dirs exist.] ************************************************
changed: [appdb1.bcfc.internal] => (item=/var/run/postgresql)

TASK [geerlingguy.postgresql : Ensure PostgreSQL is started and enabled on boot.] ****************************************
changed: [appdb1.bcfc.internal]

TASK [geerlingguy.postgresql : Ensure PostgreSQL databases are present.] *************************************************

TASK [geerlingguy.postgresql : Ensure PostgreSQL users are present.] *****************************************************

PLAY [deploy apache] *****************************************************************************************************

TASK [install apache] ****************************************************************************************************
ok: [app2.bcfc.internal]
ok: [app1.bcfc.internal]

TASK [enable apache at boot] *********************************************************************************************
ok: [app2.bcfc.internal]
ok: [app1.bcfc.internal]

TASK [start apache] ******************************************************************************************************
ok: [app2.bcfc.internal]
ok: [app1.bcfc.internal]

PLAY RECAP ***************************************************************************************************************
app1.bcfc.internal         : ok=9    changed=4    unreachable=0    failed=0
app2.bcfc.internal         : ok=9    changed=4    unreachable=0    failed=0
appdb1.bcfc.internal       : ok=20   changed=4    unreachable=0    failed=0
frontend1.bcfc.internal    : ok=6    changed=6    unreachable=0    failed=0
support1.bcfc.internal     : ok=1    changed=1    unreachable=0    failed=0
```

If you run playbook again, confirm there is no changed item in execution result.

```text
PLAY RECAP ***************************************************************************************************************
app1.bcfc.internal         : ok=9    changed=0    unreachable=0    failed=0
app2.bcfc.internal         : ok=9    changed=0    unreachable=0    failed=0
appdb1.bcfc.internal       : ok=20   changed=0    unreachable=0    failed=0
frontend1.bcfc.internal    : ok=5    changed=0    unreachable=0    failed=0
support1.bcfc.internal     : ok=1    changed=0    unreachable=0    failed=0
```


### Cleanup

Run `cleanup.yml` to cleanup and ready for you test run.

```text
$ ansible-playbook -i ../../myinventory.ini cleanup.yml

PLAY [cleanup] ***********************************************************************************************************

TASK [./roles/cleaner : reverse the enabling of sudo without tty for some ansible commands] ******************************
ok: [app1.bcfc.internal]
ok: [support1.bcfc.internal]
ok: [frontend1.bcfc.internal]
ok: [app2.bcfc.internal]
ok: [appdb1.bcfc.internal]

TASK [./roles/cleaner : Remove repo file] ********************************************************************************
changed: [support1.bcfc.internal]
changed: [frontend1.bcfc.internal]
changed: [app1.bcfc.internal]
changed: [appdb1.bcfc.internal]
changed: [app2.bcfc.internal]

TASK [./roles/cleaner : remove base tools and three tier app packages] ***************************************************
ok: [support1.bcfc.internal] => (item=[u'httpie', u'python-pip', u'haproxy', u'tomcat', u'postgresql-server'])
changed: [frontend1.bcfc.internal] => (item=[u'httpie', u'python-pip', u'haproxy', u'tomcat', u'postgresql-server'])
changed: [app1.bcfc.internal] => (item=[u'httpie', u'python-pip', u'haproxy', u'tomcat', u'postgresql-server'])
changed: [app2.bcfc.internal] => (item=[u'httpie', u'python-pip', u'haproxy', u'tomcat', u'postgresql-server'])
changed: [appdb1.bcfc.internal] => (item=[u'httpie', u'python-pip', u'haproxy', u'tomcat', u'postgresql-server'])

TASK [./roles/cleaner : Remove index.html] *******************************************************************************
ok: [appdb1.bcfc.internal]
ok: [app2.bcfc.internal]
ok: [frontend1.bcfc.internal]
ok: [app1.bcfc.internal]
ok: [support1.bcfc.internal]

PLAY RECAP ***************************************************************************************************************
app1.bcfc.internal         : ok=4    changed=2    unreachable=0    failed=0
app2.bcfc.internal         : ok=4    changed=2    unreachable=0    failed=0
appdb1.bcfc.internal       : ok=4    changed=2    unreachable=0    failed=0
frontend1.bcfc.internal    : ok=4    changed=2    unreachable=0    failed=0
support1.bcfc.internal     : ok=4    changed=1    unreachable=0    failed=0
```
