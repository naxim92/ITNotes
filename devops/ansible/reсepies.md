#ansible

``` ansible
---
- name: Verify apache installation
  hosts: webservers
  serial: 5
  vars:
    http_port: 80
    max_clients: 200
    web_service_name: httpd
  vars_files:
    - /vars/external_vars.yml
  vars_prompt:
    - name: username
      prompt: What is your username?
      private: false
  remote_user: root
  tasks:
	- name: Register loop output as a variable
	  ansible.builtin.shell: "echo {{ item }}"
	  loop:
	    - "one"
	    - "two"
	  register: echo
	
	- name: Fail if return code is not 0
	  ansible.builtin.fail:
	    msg: "The command ({{ item.cmd }}) did not have a 0 return code"
	  when: item.rc != 0
	  loop: "{{ echo.results }}"
	
	- name: Add user testuser1
	  ansible.builtin.user:
	    name: "testuser1"
	    state: present
	    groups: "wheel"
    
    - name: Ensure apache is at the latest version
      ansible.builtin.yum:
        name: httpd
        state: latest
    
    - name: Write the apache config file
      ansible.builtin.template:
        src: "{{ item.src }}".j2
        dest: "{{ item.dst }}"
	  loop:
		  - src: "/srv/httpd.conf"
		    dst: "/etc/httpd.conf"
		  - src: "/srv/site_01.conf"
		    dst: "/etc/site_01.conf"
      notify:
      - Restart "{{ web_service_name | default('httpd') }}"
    
	- name: Flush handlers
	  meta: flush_handlers
    
    - name: Ensure apache is running
      ansible.builtin.service:
        name: httpd
        state: started
    
    - name: Print a message
      ansible.builtin.debug:
        msg: 'Logging in as {{ username }}'
       delegate_to: localhost
       delegate_facts: true
       run_once: True

  handlers:
    - name: Restart apache
      ansible.builtin.service:
        name: httpd
        state: restarted
```


`{{ ansible_facts["eth0"]["ipv4"]["address"] }}`

ansible-playbook release.yml --extra-vars "version=1.23.45 other_variable=foo"