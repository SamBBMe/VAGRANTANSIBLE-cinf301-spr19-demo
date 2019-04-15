to run, install these:
    1: VAGRANT : https://www.vagrantup.com/downloads.html
                    OR through linux CLI command line: https://linuxize.com/post/how-to-install-vagrant-on-ubuntu-18-04/
    2: ANSIBLE : https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html
            NOTE: This will not work on windows. You will need a linux environment to install it. 
                    You can either use a VPS with a linux distro on it, or if you have windwows 10 pro,
                    you can start a hyperv virtual machine, and do your work in there. This will work because
                    hyperv supports nested virtualization
            NOTE: If you use a vps for this, you will need atleast 1+n cores, with n being the number of vagrant boxes you wish to use
        
Your first steps:
    1 - Find a box that you want to use. Boxes pre-made OS configurations that you can use. 
        VAGRANT BOX SITE: https://app.vagrantup.com/boxes/search

    2 - Once you have found a box that you want to use, enter/make the directory you want to the virtual box in
        and run vagrant init {BOX_NAME} -- i.e. vagrant init ubuntu/trusty64

    3 - You will now have a file called Vagrantfile in your repository. You can customize it how you would like.
        Common customizations are:
            config.vm.network "forwarded_port", guest: {GUEST_PORT (Typically a two-digit number)}, host: {HOST_PORT (Typically a four-digit number)}
                -- This will let you set your server to run on port 8080, i.e. localhost:8080
            #config.vm.provision "file", source: "{ROUTE_TO_SOURCE_FILE}", destination: "$HOME/{ROUTE_TO_DESIRED_DESTINSTION}"
                -- This lets you choose a source folder to copy over to your virtual box
                -- NOTE: There is an issue with an OpenSSH security patch, and this vagrant command. Until it gets patched it will not work
            config.vm.provision :ansible do |ansible|
		        ansible.playbook = "{PLAYBOOK_NAME}.yml"
	            end
                -- Dictates which ansible playbook you want to use. What's an ansible playbook? You'll see in the next step
    
    4 - Create a .yml file. i.e. playbook.yml. This must match the name you set in ansible.playbook = "{PLAYBOOK_NAME}.yml"
    
    5 - Configure your .yml file for running ansible. A basic format would be:
        ---
        - hosts: all
          sudo: true
          tasks:
            - name: TASK_NAME
              apt: TASK_RUN

        EXAMPLE YML (This will install the LAMP stack on your machine)
            ---
            - hosts: all
            sudo: true
            tasks:
                - name: update apt cache
                apt: update_cache=yes
                - name: install apache
                apt: name=apache2 state=present
                - name: install mysql
                apt: name=mysql-server state=present
                - name: install php
                apt: name=php5 state=present
        
        You can also do things handlers, which are similar to recallable tasks, and setting root variables.
            ---
            - hosts: all
            sudo: true
            vars:
                document_root: ROOT
            handlers:
                - name: HANDLER_NAME
                  service: HANDLER_RUN
            tasks:
                - name: TASK1_NAME
                  apt: TASK1_RUN
                - name: TASK2_NAME
                  apt: TASK2_RUN
                  notify: 
                    - HANDLER_NAME
                - name: TASK3_NAME
                  apt: TASK3_RUN

    6 - Run vagrant up, and wait for it to install
        NOTE: Notice how it downloads all of the tasks we defined, in the order we defined
    
    7 - Run vagrant status. You should see a virtual machine there with a name (Most likely default)

    8 - run vagrant ssh VIRTUAL_BOX_NAME
        -- Congratulations, you have now set up your own virtual box environment  

TO RUN MY FILES -- cd into the example directory, and run vagrant up. This will install an ubuntu virtual box wiuth a lamp stack on it for you to use.
