# Set-up haste-server


1. Install dependencies:
```
$ sudo apt-get install g++ curl libssl-dev apache2 apache2-utils git-core redis-server 
```
2. Start redis-server with default config:
```
$ systemctl enable redis-server
$ systemctl start redis-server
```
# Test redis-server reachability
```
$ redis-cli -h localhost ping
PONG
```
2. Install node.js (for node7 - replace 6 with 7)
```
$ curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
$ sudo apt-get install -y nodejs build-essential
```

3. Install and start hastebin
```
$ git clone git@github.com:shahsaifi/haste-pastebin.git
$ cd haste-server
$ npm install
$ npm install hiredis #optional
$ npm start
```
And you should have hastebin! Note: It will run on port 7777 by default

### Apache Config

1. First we need to enable mod_proxy and mod_proxy_http after installing apache2:
```
$ a2enmod proxy
$ a2enmod proxy_http
```

2. Then add to the vhost configuration file:
```
ProxyPass /haste/ http://127.0.0.1:7777/
ProxyPassReverse /haste/ http://127.0.0.1:7777/
```

3. So that the full config looks like this
```
<VirtualHost *:80>
    ServerAdmin shah@shah.com
    DocumentRoot /opt/tools/haste-server
    ServerName paste.shah.com
    ServerAlias paste.shah.com
    ProxyPass / http://127.0.0.1:7777/
    ProxyPassReverse / http://127.0.0.1:7777/
    ErrorLog /var/log/apache2/haste-server.log
    <Proxy *>
        Order deny,allow
        Allow from all
    </Proxy>
</VirtualHost>
```

4. Start apache2 service after verifying config:
```
$ apachectl configtest
Syntax OK

$ systemctl enable apache2

$ systemctl start apache2
```

Note: haste-server is running in a tmux screen with root user at the moment, feel free to run "tmux at" on the host after logging to play around.


### Follow next step to use on command line.
### Script to post content directly to Pastebin.

1. Create a script with (e.g. ~/bin/hasteit):

```
#!/bin/bash

url="http://paste.shah.com"
key="$(curl --silent --insecure --data-binary @/dev/fd/0 $url/documents | cut -d "\"" -f 4)"
echo $url"/"$key
```

2. Run on command line :
```
$  cat ~/.tmux.conf | ~/bin/pasteit
http://paste.shah.com/ahabop
```
