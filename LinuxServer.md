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