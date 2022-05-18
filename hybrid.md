## Hybrid

> 1. Run these on your Controller VM to get the dependencies:
```bash
sudo apt update -y
sudo apt upgrade -y
sudo apt install python
sudo apt install python-pip -y
sudo apt install python3-pip
alias python=python3
sudo python3 -m pip install botocore==1.26.0
sudo python3 -m pip install awscli==1.24.0 botocore==1.26.0
pip3 install boto boto3==1.23.0 botocore==1.26.0
sudo apt-get install tree -y
sudo apt-add-repository --yes --update ppa:ansible/ansible
sudo apt-get install ansible -y
```

> 2. Run these commands on Controller VM:
```bash
mkdir group_vars
cd group_vars
mkdir all
cd all
```

> 3. Run the command `sudo ansible-vault create pass.yml`.

> 4. Enter password (twice).

> 5. Enter keys as such:

```
ec2_access_key: keyhere
ec2_secret_key: keyhere
```

> 6. To save and exit vim, press esc then put in `:wq!` and press enter.

> 7. Run the command `sudo chmod 666 pass.yml`.

> 8. Try `sudo cat pass.yml` and make sure you cannot see your keys.

> 9. `cd ~/.ssh`.

> 10. `sudo nano eng119.pem`.

> 11. Enter the public key and the private key. Use this to generate the public key: `ssh-keygen -y -f ~/.ssh/file_name.pem > ~/.ssh/file_name.pub`.

> 12. `sudo chmod 400 eng119.pem`.

> 13. Check if it works with `sudo ansible-playbook tests.yml --ask-vault-pass`.

> 14. In hosts, add the following lines (replace the relevant information):
```
[local]
localhost ansible_python_interpreter=/usr/local/bin/python3

[aws]
ec2-instance ansible_host=ec2-54-216-49-215.eu-west-1.compute.amazonaws.com ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/eng119.pem
```

> 15. In `/etc/ansible`, paste this in with the correct details (after `sudo nano yaml_file.yml`):

```yaml
---
- hosts: localhost
  connection: local
  gather_facts: True
  become: True
  vars:
    key_name: eng119
    region: eu-west-1
    image:  ami-01c0552849d892535
    id: "eng110_sam_app_from_ansible2"
    sec_group: "sg-0e9e2d55bfe179cb4"
    subnet_id: "subnet-0429d69d55dfad9d2"

    ansible_python_interpreter: /usr/bin/python3
  tasks:

    - name: Facts
      block:

      - name: Get instances facts
        ec2_instance_facts:
          aws_access_key: "{{ec2_access_key}}"
          aws_secret_key: "{{ec2_secret_key}}"
          region: "{{ region }}"
        register: result

    - name: Provisioning EC2 instances
      block:

      - name: Upload public key to AWS
        ec2_key:
          name: "{{ key_name }}"
          key_material: "{{ lookup('file', '~/.ssh/{{ key_name }}.pub') }}"
          region: "{{ region }}"
          aws_access_key: "{{ec2_access_key}}"
          aws_secret_key: "{{ec2_secret_key}}"

      - name: Provision instance(s)
        ec2:
          aws_access_key: "{{ec2_access_key}}"
          aws_secret_key: "{{ec2_secret_key}}"
          assign_public_ip: true
          key_name: "{{ key_name }}"
          id: "{{ id }}"
          vpc_subnet_id: "{{ subnet_id }}"
          group_id: "{{ sec_group }}"
          image: "{{ image }}"
          instance_type: t2.micro
          region: "{{ region }}"
          wait: true
          count: 1
          instance_tags:
            Name: eng110_sam_node_app_from_ansible2

      tags: ['never', 'create_ec2']
```

> 16. `sudo nano set_up_ec2.yml` and insert these commands:

```yaml
---
        # where do we want to install
- hosts: aws

        # get the facts
  gather_facts: yes

        # changes access to root user
  become: true

  tasks:

        # Purge Nginx
  - name: Purge Nginx
    shell: |
      sudo apt-get purge nginx nginx-common -y

        # Install Nginx
  - name: Install nginx
    apt: pkg=nginx state=present

        # Set up reverse proxy

  - name: Remove Nginx default file
    file:
      path: /etc/nginx/sites-enabled/default
      state: absent

  - name: Create file reverse_proxy.config with read and write permissions for everyone
    file:
      path: /etc/nginx/sites-enabled/reverse_proxy.conf
      state: touch
      mode: '666'

  - name: Inject lines into reverse_proxy.config
    blockinfile:
      path: /etc/nginx/sites-enabled/reverse_proxy.conf
      block: |
        server{
          listen 80;
          server_name development.local;
          location / {
              proxy_pass http://localhost:3000;
              proxy_http_version 1.1;
              proxy_set_header Upgrade $http_upgrade;
              proxy_set_header Connection 'upgrade';
              proxy_set_header Host $host;
              proxy_cache_bypass $http_upgrade;
          }
        }

        # Links the new configuration file to NGINX’s sites-enabled using a command.

  - name: link reverse_proxy.config
    file:
      src: /etc/nginx/sites-enabled/reverse_proxy.conf
      dest: /etc/nginx/sites-available/reverse_proxy.conf
      state: link

  - name: Restart Nginx
    shell: |
      sudo systemctl restart nginx

        # Gets all the dependencies

  - name: Install software-properties-common
    apt: pkg=software-properties-common state=present

  - name: Add nodejs apt key
    apt_key:
      url: https://deb.nodesource.com/gpgkey/nodesource.gpg.key
      state: present

  - name: Install nodejs
    apt_repository:
      repo: deb https://deb.nodesource.com/node_13.x bionic main
      update_cache: yes

  - name: Install nodejs
    apt:
      update_cache: yes
      name: nodejs
      state: present

  - name: Install npm
    shell: |
      cd app/
      npm install

  - name: Install pm2
    npm:
      name: pm2
      global: yes

  - name: run app
    shell: |
      cd app/
      npm start
```

> 17. Run the command `sudo ansible-playbook set_up_app.yml --ask-vault-pass`.

> 18. Go to the EC2's public IP.