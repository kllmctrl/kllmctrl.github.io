---
title: Git Server - Ubuntu
date:   2014-3-5 00:00:00 +0700
categories: [Tech, Tool]
tags: [gitserver]
---


>Git Server 在Ubuntu上部署和使用。


### Git Server Install


#### install openssh server and client

```shell
sudo apt-get install openssh-server openssh-client
```


#### install git server

```shell
sudo apt-get install git-core
```


#### config git server

new a git user to git server

```shell
sudo useradd -m git
sudo passwd git
```

init global name

```shell
git config --global user.name "kllmctrl"
git config --global user.email "kllmctrl@gmail.com"
```


#### install python

```shell
sudo apt-get install python-setuptools
```

#### install gitosis

```shell
cd /tmp
git clone https://github.com/res0nat0r/gitosis.git
cd gitosis
sudo python setup.py install
```


### Git Server Authorization


#### mac authorization

```shell
ssh-keygen -t rsa
```

#### config keygen on git server

on mac console

```shell
sudo scp .ssh/id_rsa.pub git@192.168.176.136:/tmp
```

on git server console

```shell
sudo chmod a+r /tmp/id_rsa.pub
sudo -H -u git gitosis-init < /tmp/id_rsa.pub
```

so /home/git will appear gitosis and repositores(contain gitosis-admin.git)

set root user：sudo passwd root
sign in root：su root

set some files to read

```shell
sudo chmod 755 /home/git/repositories/gitosis-admin.git/hooks/post-update
```


#### test a git

```shell
su git
cd /home/git/repositories
mkdir test.git
cd test.git
git init --bare
```

on mac console

```shell
cd /home/work/config-git-server
git clone git@192.168.176.136:gitosis-admin.git
cd gitosis-admin.git
```

put Authorization users.pub to keydir floders ,example A.pub B.pub

then modify gitosis.conf

```shell
[gitosis]

[group gitosis-admin]
writable = gitosis-admin
members = weedcatch@weedcathtekiMac.local.pub

[group test]
writable = test
members = weedcatch@weedcathtekiMac.local.pub A B
```

comit config to git server

```shell
cd gitosis-admin
git add .
git commit -am "add test project and users"
git push origin master
```


### GitWeb brower the git server


#### install apache2

```shell
sudo apt-get install apache2
```


#### install gitweb

```shell
sudo apt-get install gitweb
```

if no the gitweb floders then you can clone the git.git and copy the git/gitweb to path


#### config gitweb

```shell
sudo cp /etc/apache2/conf.d/gitweb /etc/apache2/conf-available/gitweb.conf
cd /etc/apache2/conf-enabled
sudo ln -s ../conf-available/gitweb.conf
```

modify:gitweb.conf

```shell
#<VirtualHost *:8056>
	Alias /gitweb /usr/share/gitweb
	<Directory /usr/share/gitweb>
		Options +FollowSymLinks +ExecCGI
		AddHandler cgi-script .cgi
	</Directory>
	ServerName localhost
#</VirtualHost>

```


cd /var/www/
sudo ln -s /usr/share/gitweb/* .

then modify：
sudo vi /etc/gitweb.conf

``` shell
	# path to git projects (<project>.git)
	#$projectroot = "/var/cache/git";
	$projectroot = "/home/git/repositories";
	# directory to use for temp files
	$git_temp = "/tmp";
	# target of the home link on top of all pages
	$home_link = $my_uri || "/";
	# html text to include at home page
	$home_text = "indextext.html";
	# file with project list; by default, simply scan the projectroot dir.
	$projects_list = $projectroot;
	# stylesheet to use
	@stylesheets = ("/gitweb/static/gitweb.css");
	# javascript code for gitweb
	$javascript = "/gitweb/static/gitweb.js";
	# logo to use
	$logo = "/gitweb/static/git-logo.png";
	# the 'favicon'
	$favicon = "/gitweb/static/git-favicon.png";
	# git-diff-tree(1) options to use for generated patches
	#@diff_opts = ("-M");
@diff_opts = ();

```

modify:/etc/apache2/conf.d/gitweb

and modify:

/var/www/gitweb.cgi
css.path.... add /gitweb/static/....
etc.


restart apacha2

```shell
sudo a2enmod cgi
sudo service apache2 restart
```




#### brower site

``` shell
http://localhost/cgi-bin/gitweb.cgi
```



### Use git for users


#### common

```shell
git clone git@192.168.176.136:test.git

```

#### Tower on mac

ssh://192.168.176.136/test.git
user:git
pwd:.....
