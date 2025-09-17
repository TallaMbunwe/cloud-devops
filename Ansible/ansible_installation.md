# Deploying Ansible

This is a guide for installing Ansible for configuration management, application deployment and task automation.

### Pre-requisites

1. 2 Virtual Machine instances (Control and Workstaion nodes)

### Installation steps:
#### on Azure VM Control node

Install Ansible on the Control Host

1. Log in to the Control Host server using ssh, cloud_user, and the provided Public IP address and password:

	```sh
    ssh cloud_user@<PUBLIC IP>
	```

    You may want to check Linux version by looking at the content of the /etc/os-release file:

	```sh
    cat /etc/os-release
	```

 2. Install epel-release (Extra Packages for Enterprise Linux) and enter y when prompted:

	```sh
    sudo yum install epel-release
	```

 3. Install Ansible and enter y when prompted:

	```sh
    sudo yum install ansible
	```

Important Notice

    The following information has been provided by Red Hat, but is outside the scope of the posted Service Level Agreements and support procedures.
    Installing unsupported packages does not necessarily make a system unsupportable by Red Hat Global Support Services
        However, Red Hat Global Support Services will be unable to support or debug problems with packages not shipped in standard RHEL channels.
    Installing packages from EPEL is done at the user's own risk.
    The EPEL repository is a community supported repository hosted by the Fedora Community project.
    The EPEL repository is not a part of Red Hat Enterprise Linux and does not fall under Red Hat's Production Support Scope of Coverage. 
	The repository is considered an optional repository and is not tested by Red Hat quality engineers.



Create an ansible User

Now, we need to create the ansible user on both the control host and workstation host.

1. On the control node:

	```sh
    sudo useradd ansible
	```

2. Connect to the workstation node using the provided password:

	```sh
    ssh workstation
	```


3. Add the ansible user to the workstation and set a password for the ansible user. We need to make sure it is something we will remember:

	```sh
    sudo useradd ansible
    sudo passwd ansible
    logout
	```

Configure a Pre-Shared Key for Ansible

With our user created, we need to create a pre-shared key that allows the user to log in from control to workstation without a password.

1. Change to the ansible user:

	```sh
    sudo su - ansible
	```

2. Generate a new SSH key, accepting the default settings when prompted:

	```sh
    ssh-keygen
	```

3. Copy the SSH key to workstation, providing the password we created earlier:

	```sh
    ssh-copy-id workstation
	```

4. Test that we no longer need a password to log in to the workstation:

	```sh
    ssh workstation
	```

5. Once we succeed at logging in, log out of workstation:

	```sh
    logout
	```

Configure the Ansible User on the Workstation Host

Our next job is to configure the ansible user on the workstation host so that that they may sudo without a password.

1. Log in to the workstation host as cloud_user using the password provided by the lab:

    ```sh
    ssh cloud_user@workstation
	```

2. Edit the sudoers file:

	```sh
    sudo visudo
	```

3. Add this line at the end of the file:

    ```sh
    ansible       ALL=(ALL)       NOPASSWD: ALL
	```

4. Save the file:
	```sh
    :wq
	```
    
5. Log out of workstation:
	```sh
    logout
	```
    



Create a Simple Inventory

Next, we need to create a simple inventory, /home/ansible/inventory, consisting of only the workstation host.

1. On the control host, as the ansible user, run the following commands:

	```sh
    vim /home/ansible/inventory
	```
    

2. Add the text "workstation" to the file and save using :wq in vim.


Write an Ansible Playbook

We need to write an Ansible playbook into /home/ansible/git-setup.yml on the control node that installs git on workstation, then executes the playbook.

1. On the control host, as the ansible user, create an Ansible playbook:
	```sh
    vim /home/ansible/git-setup.yml
	```
    

2. Add the following text to the file:
	```sh
    --- # install git on target host
    - hosts: workstation
      become: yes
      tasks:
      - name: install git
        yum:
          name: git
          state: latest
	```

3. Save and exit the file (:wq in vim).

4. Run the playbook:
	```sh
    ansible-playbook -i /home/ansible/inventory /home/ansible/git-setup.yml
	```
    
5. Verify that the playbook ran successfully:
	```sh
    ssh workstation
    which git
    ```


Conclusion
