# id_rsa

使用 Git 仓库时，我们一般会使用 SSH 的方式去拉取代码


下面我们将介绍如何进行 SSH Key 的配置

##### 第一步 - Go to SSH-Key path

```
cd ~/.ssh
```

##### 第二步 - Generate SSH-Key

```
ssh-keygen -t rsa -C "youremail@email.com"
```

##### 第三步 - Input SSH-Key name

##### 第四步 - Input SSH-Key password

##### 第五步 - Copy public key

```
cat id_rsa_hostname.pub
```

##### 第六步 - Copy public key

##### 第七步 - Test

```
ssh -T git@github.com
```

##### 第八步 - Configure multiple SSH-Key

```
vim config


# github
Host github.com
    User yourUserName
    IdentityFile ~/.ssh/id_rsa_hostname
    IdentitiesOnly yes

```
