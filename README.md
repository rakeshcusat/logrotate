[![Build Status](https://travis-ci.org/rakeshcusat/logrotate.svg?branch=master)](https://travis-ci.org/rakeshcusat/logrotate)
Logrotate
=========

It installs `Logrotate` and set the configuration.

Requirements
------------

This role should be installed as galaxy role in your Ansible project.

Dependencies
------------

There is no dependencies on other role.

Role Variables
--------------
* `logrotate_configs`: Define it in Ansible project. It is a list of configuration dictionary for different log files.

  * `name`: Name of the logrotate file. Recommended to keep the name as the service name.
  * `log_file_path`: Log file path which should be rotated.
  * `frequency`: Frequency of logrorate. It should be either one of these `daily`, `weekly`, `monthly` or `yearly`.
  * `size`: Log files are rotated when they grow bigger then size. `K`, `M` and `G` can be used for Kilobytes, Megabytes, and Gigabytes e.g 100M.  
  * `rotate_count`: Log files will be rotated these many times before get deleted. It expect an integer value.
  * `email`: Email address where log will be sent.
  * `missing_ok`:  If the log file is missing, go on to the next one without issuing an error message.
  * `compress`: It is a dictionary configuration which contains three more parameters which are optional. `delay` to delay compression of the log file. `command` to compress the log file. By default, logrotate `gzip` to compress the file. `options` it is command options passed for the compress command. It is an optional argument.
  * `rotated_file_extension`: Rotated file extension. By default it is `.gz`.
  * `copy`: To copy the log file.
  * `copy_truncate`: Truncate the original log file in place after creating a copy.
  * `create`: After creating log file it sets the given mode, user and group. It is a dictionary configuration which contains three fields `mode`, `owner`, and `group`. Please check the example.
  * `old_directory`: Logs are moved to into this directory for rotation.
  * `scripts`: It is a dictionary configuration. It can contains three optional fields `shared`, `prerotate` and `postrotate`. If you want to shared the script with other rotated then set this field. `prerotate` accepts a block of script lines and executed before rotating the files. `postrotate` also accepts a block of script lines and execute them after rotating the files. Please check examples.
  * `actions`: It is a dictionary configuration. It can contains two optional fields `first` and `last`. `first` accepts a block of commands which will be executed before executing the prerotate scripts whereas `last` also accepts a block of command which will be executed after executing the post script.

* `logrotate_version`: Version of the logrotate which should be deployed. Currently it is set as `3.7.8-6ubuntu5` but can be overwritten in the Ansible project to install the desired version of logrotate.


Example Playbook
----------------

Setting up the logrotate configuration for apache2 and tomcat.
    
    logrotate_configs:
        - name: apache2
          log_file_path: "/var/log/apache2/*.log"
          frequency: "weekly"
          size: 1024M
          rotate_count: 52
          email: rakesh.kumar@code4reference.com
          missing_ok: true
          compress:
              delay: true
          not_if_empty: true
          create:
              mode: 640
              owner: root
              group: adm
          scripts:
              shared: true
              prerotate: |
                  if [ -d /etc/logrotate.d/httpd-prerotate];then \
                      run-parts /etc/logrotate.d/httpd-prerotate; \
                  fi; \
              postrotate: |
                /etc/init.d/apache2 reload > /dev/null
          actions:
              first: |
                  echo "First action:  `date`"
              last: |
                  echo "End action: `date`"
        - name: tomcat
          log_file_path: "/your/applicaiton/log/file/path"
          frequency: 'daily'
          rotate_count: 7
          compress:
              delay: false

For installing this role the following line should be in the requirements.yml

    - src: https://github.com/rakeshcusat/logrotate
      version: master
      scm: git
      name: logrotate
You can use the following command to installed the role in `shared-roles` directory. You can modify this command based on your requirements.
     `ansible-galaxy install -r requirements.yml -p shared-roles --force`

License
-------

MIT

Author Information
------------------

Rakesh Kumar [Code4Reference.com](http://code4reference.com/)
