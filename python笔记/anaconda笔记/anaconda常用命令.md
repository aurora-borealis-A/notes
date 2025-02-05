## 环境管理

- `conda env list`：列出所有的环境。
  - `conda info --envs`：同样也是列出所有的环境。

- `conda create -n 新环境名 [python=x.x]`：创建新环境，并指定新环境中python的版本。
- `conda remove -n xxxxx(名字) --all`：完全删除一个环境
  - `-n`：表示指定环境的名称
  - -all：表示删除指定环境中的包。
    - 如果不加-all，则效果只是删除环境中的最近安装的一个包。
    - 如果加了-all，则删除环境中所有的包，同时删除指定环境。

- `conda activate 环境名`：进入指定环境。

- `conda deactivate`：退回到base环境。

## 环境中

- `conda list`：列出当前环境中所有的软件包。