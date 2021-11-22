# Git LFS


### Github 单文件超过 100M 大文件上传解决

- 首先先进行安装，详细步骤见[官网](https://git-lfs.github.com/)；

- 运行 ```git lfs install``` 对 Git LFS 进行安装，然后开始指定文件类型，这里以 .pdf 文件类型为例 ```git lfs track "*.pdf"```，也可指定固定路径 ```git lfs track "Test/Test1/Test.framework"```

- 如果使用 SourceTree，可以在顶部的菜单栏中的仓库 -> Git LFS -> 添加类型内容为 track 后面的类型或路径

- ```git add .gitattributes```

- commit 后，运行 ```git lfs ls-files``` 可列举出当前被检测出的文件  

- push ，可能报 The remote end hung up unexpectedly，可以通过运行 ```git config --global http.postBuffer 524288000``` 命令解决


> 码云不支持 Git LFS， GitLab 支持大文件上传，目前 Git LFS 的总存储量为 1G 左右，超过需要付费
