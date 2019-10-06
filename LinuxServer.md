### Linux
* Enable proxy for Yum!
```
vim /etc/yum.conf
```
then add these lines to the file and save it
```
proxy=http://<Proxy-Server-IP-Address>:<Proxy_Port>
proxy_username=<Proxy-User-Name>
proxy_password=<Proxy-Password> 
```

### Tmux cheat sheet
* create new session :
```
tmux new -s myname
```
* attach to a session :
```
tmux a -t myname
```
* see list of sessions :
```
tmux ls
```
* kill session :
```
tmux kill-session -t myname
```
* In tmux, hit the ctrl+b, then :
```
c  create window
w  list windows
n  next window
p  previous window
f  find window
,  name window
&  kill window
d  detach
?  list shortcuts
```

### Git
* Enable proxy for Git!
```
git config --global http.proxy http://proxyuser:proxypwd@proxy.server.com:8080
```
* Check git proxy
```
git config --global --get http.proxy
```
* Disable git proxy
```
git config --global --unset http.proxy
```
