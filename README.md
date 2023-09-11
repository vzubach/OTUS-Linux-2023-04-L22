### OTUS-Linux-2023-04-L22 | PAM

1. Созданы пользователи *otusadm* и *otus*. Создана группа admin, в неё добавлены пользователи: otusadm,root и vagrant;

		[root@Centos8 yum.repos.d]# cat /etc/group | grep -e ^admin  
		min:x:1003:otusadm,root,vagrant

2. Создан скрипт для анализа даты и пользователя, который будет логиниться по ssh:

		#!/bin/bash
		if [ $(date +%a) = "Sat" ] || [ $(date +%a) = "Sun" ]; then
 		if getent group admin | grep -qw "$PAM_USER"; then
    	    exit 0
    	  else
    	    exit 1
    	fi
  		else
    	exit 0
		fi

3. Настройки PAM для sshd следующие:
	
	>[root@Centos8 etc]# cat /etc/pam.d/sshd </br>
	>#%PAM-1.0</br>
	>auth       substack     password-auth</br>
	>auth	   include      postlogin</br>
	>account    required     pam_nologin.so</br>
	>**account	   required		pam_exec.so /usr/local/bin/login.sh**</br>
	>account    include      password-auth</br>
	>-password   include      password-auth</br>
	># pam_selinux.so close should be the first session rule</br>
	>session    required     pam_selinux.so close</br>
	>session    required     pam_loginuid.so</br>
	># pam_selinux.so open should only be followed by sessions to be executed in the user context</br>
	>session    required     pam_selinux.so open env_params</br>
	>session    required     pam_namespace.so</br>
	>session    optional     pam_keyinit.so force revoke</br>
	>session    optional     pam_motd.so</br>
	>session    include      password-auth</br>
	>session    include      postlogin</br>
	>[root@Centos8 etc]# </br>

4. Проверям что пользователь *otusadm* может подключиться по SSH в субботу, а пользователь otus - нет.

![Пруф](2023-09-11_18_15_10-Window.png)





