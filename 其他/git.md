git

git stash save "first"          -----保存并隐藏目前的修改，这样可以进行其他修改工作

git stash apply stash@{1} -----重新将保存的更改进行应用

git stash list                       -----查看所有保存

git stash pop                     -----弹出最新保存的修改，会删除这个弹出的修改

在push代码前，可以先保存修改，然后pull远程的代码到本地，再将修改放出来查看是否有冲突并进行修改，然后再add  commit    push

https://www.cnblogs.com/zndxall/archive/2018/09/04/9586088.html

---------------------------------------------------------------------



