> 使用 vdev 工具开发一个内部模块的流程

1. [全局安装 vdev 工具](https://lark.alipay.com/vision/component-dev/dev_tools)
    * tnpm install @ali/visualengine-devtools -g
2. 新建项目文件夹，并在对应文件夹目录执行 `yo uxcore` 命令初始化项目，一路回车即可
3. 新建远程仓库，并使用如下命令将本地文件和远程仓库关联（注意 `remote-repository-git-url` 替换为你的远程仓库地址）
    * git add .
    * git commit -m "initial commit"
    * git remote add origin `remote-repository-git-url`
4. 发布已开发好的模块
    * tnpm login
    * vdev build
    * vdev publish
    



