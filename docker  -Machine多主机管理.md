docker  -Machine多主机管理

路径：

https://docs.docker.com/machine/install-machine/

```
base=https://github.com/docker/machine/releases/download/v0.16.0 &&
  curl -L $base/docker-machine-$(uname -s)-$(uname -m) >/tmp/docker-machine &&
  sudo mv /tmp/docker-machine /usr/local/bin/docker-machine &&
  chmod +x /usr/local/bin/docker-machine
```

下载好之后

docker-machine -v  查看版本号

到网页

![1574126752184](C:\Users\Administrator.DESKTOP-Q44R3VB\AppData\Roaming\Typora\typora-user-images\1574126752184.png)



vim /etc/bash_completion.d/docker-machine-prompt.bash

