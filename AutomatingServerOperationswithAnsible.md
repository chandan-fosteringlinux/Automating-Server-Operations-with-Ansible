# **Automating Server Operations with Ansible**

### **Overview**

Ansible is a powerful automation tool that simplifies performing tasks on remote servers. This presentation will guide you through key operations using Ansible, from inventory configuration to performing tasks like connectivity checks, software installation, and file management on remote machines.

---

## **1\. Setting Up Ansible Inventory**

### **Inventory File Configuration**

The inventory file (`hosts` file) defines the remote servers Ansible will manage. We provide the server address along with the remote user credentials.

#### **Example:**

`192.168.122.183 ansible_user=root`

This setup tells Ansible to connect to the server with IP `192.168.122.183` using the `root` user.

#### **Verify Inventory Setup**

Use the command below to check if the inventory configuration is correct:

`ansible-inventory --list`

**Output:**
  
```
{  
    "_meta": {  
        "hostvars": {  
            "192.168.122.183": {  
                "ansible_user": "root"  
            }  
        }  
    },  
    "all": {  
        "children": ["ungrouped"]  
    },
    "ungrouped": {
        "hosts": ["192.168.122.183"]
    }
}
```

This output confirms the inventory is correctly set up, with `192.168.122.183` listed under the `ungrouped` section and `root` as the user.

---

## **2\. Testing Server Connectivity**

Ansible’s `ping` module checks the connection to remote servers.

#### **Command:**
 
`ansible all -m ping`

#### **Sample Output:**
``` 
192.168.122.183 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/libexec/platform-python" 
    },
    "changed": false,
    "ping": "pong" 
}
```
### **Explanation:**

* **Host Connection:** Ansible connected successfully to `192.168.122.183`.  
* **Module Used:** The `ping` module checks connectivity.  
* **Python Interpreter:** Ansible automatically detected the Python interpreter (`/usr/libexec/platform-python`) on the remote host.  
* **Ping Response:** The "pong" response confirms the connection.  
* **No Changes Made:** The `"changed": false` means it was a check, not a modification.

---

## **3\. Writing and Executing Ansible Playbooks**

Ansible Playbooks define tasks to be executed on remote servers. Here are examples for various tasks:

### **A. Connectivity Test Playbook**

This simple playbook tests the connection to remote servers and prints a message if successful.
``` 

---  
- name: ConnectionTestPlayBook  
  hosts: all
  tasks:  
    - name: Test Connectivity
      ping:
        
    - name: "Print output"
      debug: msg="Alright"
```


#### **Running the Playbook:**

`ansible-playbook CheckConnectionToRemotePlaybook.yml`

#### **Output:**

```
PLAY [ConnectionTestPlayBook] **************************************************

TASK [Gathering Facts] *********************************************************
ok: [192.168.122.183]

TASK [Test Connectivity] *******************************************************
ok: [192.168.122.183]

TASK [Print output] ************************************************************
ok: [192.168.122.183] => {
    "msg": "Alright"
}
```

### **B. Installing Software on Remote Servers**

Let’s install and start the Nginx web server on a remote machine using a playbook.

```
---
- name: Install and start the service
  hosts: all

  tasks:  
    - name: Installing Nginx  
      yum:
        name: nginx  
        state: present

    - name: Starting the Nginx service  
      service:
        name: nginx
        state: started  
        enabled: true
```

#### **Running the Playbook:**

`ansible-playbook nginx_install.yml`

#### **Output:**

```
PLAY [Install and start the service] *******************************************

TASK [Installing Nginx] ********************************************************
ok: [192.168.122.183]

TASK [Starting the Nginx service] **********************************************
ok: [192.168.122.183]
```

### **C. Copying Files to a Remote Server**

You can also use Ansible to copy files from your local machine to remote servers.

```
---  
- name: Copying files to remote
  hosts: all

  tasks: 
    - name: Copy files
      copy:
        src: /home/cs/Desktop/file.txt
        dest: /tmp/
```

#### **Running the Playbook:**
  
`ansible-playbook copy_file.yml`

#### **Output:**

```
PLAY [Copying files to remote] *************************************************

TASK [Gathering Facts] *********************************************************
ok: [192.168.122.183]

TASK [Copy files] **************************************************************
ok: [192.168.122.183]

PLAY RECAP *********************************************************************
192.168.122.183            : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```

---

## **4\. Handling File Permissions**

When copying files, Ansible can manage file permissions too.

```
---  
- name: Copying files with permissions
  hosts: all

  tasks:  
    - name: Copy files with permissions
      copy:  
        src: /home/cs/Desktop/file.txt  
        dest: /tmp/  
        owner: cs  
        group: user  
        mode: '0777'
```

### **Overriding Files and Making Backups**

```
---  
- name: Override existing file and backup
  hosts: all

  tasks:  
    - name: Copy and backup file  
      copy:
        src: /home/cs/Desktop/myfile.txt  
        dest: /home/cs/files/  
        mode: 'ugo=rw'  
        backup: true
```

This command copies the file and automatically creates a backup of the existing file if it already exists.

The result in server terminal is as follows :-

```
[root@localhost files]# ls -ltr
total 8
-rw-r--r--. 1 root root 12 Sep 27 07:45 myfile.txt.5176.2024-09-28@02:43:35~
drwxrw-r--. 2 root root  6 Sep 27 08:17 new_folder
-rw-rw-rw-. 1 root root 36 Sep 28 02:43 myfile.txt
[root@localhost files]# 

```

---

### **Conclusion**

Ansible provides a powerful framework for automating operations on remote servers. From configuring inventories, testing connectivity, installing software, to copying and managing files, Ansible playbooks make automation streamlined and efficient.

---

This document can be used as a foundation to further explore and expand your Ansible automation processes.

