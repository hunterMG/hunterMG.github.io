---
title: Write blog with Hexo
date: 2018-01-24 19:02:13
categories: note
tags:
- hexo
- github
- webhook
- submodule
- https
---

## Abstract
`hexo` to write blog with Markdown syntax  
`github` to host the source files  
`webhook` to automaticlly update the blog in VPS  
`git submodule` to manage the Hexo theme  

## Setup
Add a new github respository(name it : github-id.github.io).  
On your PC:
```sh
mkdir blog
cd blog
npm install hexo-cli -g # Node.js required
hexo init
git init
# use branch 'hexo' to store the source files, branch 'master' to store the html files.
git checkout -b hexo
git remote add origin git@github.com:github-id/github-id.github.io.git
hexo new "my-first-post"
# write blog
hexo g # generate static html files
hexo s # preview the blog on your pc(default: http://localhost:4000)
```
Config the github repository in `_config.yml` :
```yml
deploy:
  type: git
  repo: git@github.com:github-id/github-id.github.io.git
  branch: master
```
Deploy the blog.
```sh
hexo d # push html files to the master branch.
```
Now open https://github-id.github.io, you can see your blog.  

## Webhook
Add a webhook of your blog's github respository(branch `master`).(url example : `http://your-host/hook.php`)  
Edit `hook.php` in your VPS as follows:
```php
<?php
error_reporting(1);

$secret = 'blog'; # must be same as the secret you type on github
$signature = $_SERVER['HTTP_X_HUB_SIGNATURE'];
$json = json_decode(file_get_contents('php://input'), true);
$cmd = "./deploy.sh";
if($signature){
    $hash = "sha1=" . hash_hmac('sha1', $HTTP_RAW_POST_DATA, $secret);
    if (strcmp($signature, $hash) == 0 && array_key_exists('ref', $json)) {
        echo shell_exec($cmd);
        exit();
    }
}
header('X-PHP-Response-Code: 404', true, 404);
?>
```

Edit `deploy.sh`
```sh
#!/bin/bash

cd ../blog
echo `date +%D\ %T`>> ../hook/hook.log
git reset --hard origin/master
git clean -f
git pull origin master >> ../hook/hook.log 2>>../hook/hook.log
```

Add deploy key(Read-only access is just fine) for the VPS in your respository:
(The example shown below is for `apache`)
```sh
mkdir /var/www/.ssh
chown -R apache:apache /var/www/.ssh
sudo -Hu apache ssh-keygen -t rsa
cat /var/www/.ssh/id_rsa.pub
```
Copy the public key to add it in github respository.

Clone the repository on VPS folder `blog`:
```sh
mkdir blog
chown -R apache:apache blog
sudo -Hu apache git clone git@github.com:github-id/github-id.github.io.git blog
``` 

Test webhook on your PC:  
```sh
cd blog
git checkout master
git commit -m "hook test" --allow-empty
git push
```
Then check the `hook.log` on VPS or just open http://your-donmain-name to check the blog.
## Theme -- submodule
Fork the theme respository.(such as `hexo-theme-next`)  
On your PC:
```sh
cd blog
git checkout hexo
git submodule add git@github.com:github-id/hexo-theme-next.git theme/next 
```
So you can manage your blog content and theme in two respository separately.

## https
Apply a SSL CA on `freessl.org`;  
Download the Certificate files(`domain.crt`, `domain.ca.crt`, `private.key`);  
Install necessary module:
```sh
yum install mod_ssl openssl
```
Edit /etc/httpd/conf.d/ssl.conf:
```sh
SSLCertificateFile /path-to/domain.ca.crt
SSLCertificateKeyFile /path-to/private.key
```
Restart apache:
```sh
service httpd restart
```
## After you changed your PC :
```sh
git clone git@github.com:github-id/github-id.github.io.git
cd github-id.github.io
git checkout hexo
git submodule init
git submodule update
npm install
hexo new "new-post-name"
```

