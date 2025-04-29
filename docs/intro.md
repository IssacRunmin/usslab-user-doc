---
sidebar_position: 1
sidebar_label: '写在前面'
title: '写在前面'
---

这是一些使用USSLAB计算集群的**注意事项**

## 📢通知：五一期间网络文件系统磁盘整理

由于`/mnt/home`共享文件系统接近满载，负载均衡没有调节好，导致所有服务器使用的硬盘只是集群的一两个（其他都是满载的；之前都是硬盘满一个增加一个，所以导致读取硬盘集中在新硬盘）。这导致了很大的性能问题：登录节点卡顿，任务排长队，总体gpu util%不高甚至接近0%。

为彻底解决此问题，缓解硬盘瓶颈，计划五一期间对网络文件系统进行数据迁移和升级。2025年4月30日-5月5日，对网络文件系统磁盘整理，开放`/mnt/data`并逐步限制使用大小。期间可能会影响到文件的读写操作，请提前做好数据备份。

### 具体安排：
1. 4月30日-5月1日：`/mnt/data`：增加存储空间(30TB)，迁移各服务器旧数据（服务器未成立集群时候的/home目录）至`/mnt/data/old_home`中，预计增加到80TB。
    - [x] star: /home, 共8TB
    - [ ] hermit: /home, 共16TB；/mnt/NewSSD，共8TB
    - [ ] strength: /home, 共10TB
    - [ ] sun: /mnt/local_home, 共16TB
    - [x] temperance: /home，共425GB
2. 5月1日至5月3日：`/mnt/data`旧目录的文件元数据更改。把文件分配给各个小伙伴，完成后大家可以访问`/mnt/data/old_home/<server>/<user?>`历史文件，其中`<server>`是原来的服务器，`<user?>`是之前的用户名，有些匹配不上的可以后续联系管理员进行owner claim。
2. 5月2日至5月3日：`/mnt/home`：以用户为单位进行磁盘整理，对文件进行条带化（即，一个文件均匀分布在多个硬盘，提高整体速率）
3. 5月4日至5月5日：将在节末（5月4日或5月5日，具体时间段另行提前通知）进行底层文件系统升级，期间会导致文件系统不可用。（具体是否需停机升级另行通知，也许一个假期搬不完文件呢）
4. 后续安排：将机械硬盘从`/mnt/home`退出，加入到`/mnt/data`中；限制`/mnt/home`每个用户能用的大小

### Hint

后续将限制home目录的Quota，数据集等量大的数据可以放在/mnt/data中。大家可以养成代码/数据分开的习惯，可以用快捷方式(`ln -s <dst> <src>`)的方式来达到同一个逻辑目录下读取数据的目的。
例子：
```bash
# lately, I will create such symlink for each user's home
ourunmin@moon:~$ ls -l data
lrwxrwxrwx 1 root root 18 Mar  4 11:26 data -> /mnt/data/ourunmin
ourunmin@moon:~$ mkdir data/your_project # store project data here
ourunmin@moon:~$ mkdir your_project # your project repo
ourunmin@moon:~$ cd your_project
ourunmin@moon:~/your_project$ ln -sv ~/data/your_project data
'data' -> '/mnt/home/ourunmin/data/your_project'
ourunmin@moon:~/your_project$ touch your_code_here
ourunmin@moon:~/your_project$ dd if=/dev/zero of=data/your_large_file_here bs=1K count=1024
ourunmin@moon:~/your_project$ tree -l
.
├── data -> /mnt/home/ourunmin/data/your_project
│   └── your_large_file_here
└── your_code_here

1 directory, 2 files
```
这样你可以在`/mnt/home`目录下访问到存储在`/mnt/data`的文件。

（不知道例子的意思？复制上面的文本和例子让大模型解释一下吧(⌒▽⌒)）

### Q&A: 

**Q1: `/mnt/home`和`/mnt/data`有什么不同？**

A1: 两个都是共享文件系统，底层硬盘和机制有所不同：
1. `/mnt/home`为固态硬盘，`/mnt/data`为机械硬盘，存储速度`/mnt/home`更快，适合代码/环境存储（但后续会限制磁盘大小）；`/mnt/data`量大，适合数据集存储。
2. `/mnt/home`使用1:1冗余，1个计算节点掉线不会影响数据读写，但文件在此系统里占两倍大小；`/mnt/data`则无冗余，计算节点掉线可能会导致数据读写卡住（IO操作无响应，程序进入'D'状态）。请注意冗余不是备份，用户删除文件将是彻底删除，无法恢复。


## 用前需知

- 需要有基本的 Linux/Unix 系统使用经验，会进行基础的Linux操作
- 请先在自己的电脑上将计算程序编写调试正确，再到超算上提交运行
- 服务器暂无数据备份，请勿在超算存储长期存放数据，确保计算结果等重要数据至本地保存
- 误删除、误覆盖的文件无法恢复
- 有问题请email: ourunmin@zju.edu.cn Before asking, refer to [this](https://github.com/selfteaching/How-To-Ask-Questions-The-Smart-Way/blob/master/How-To-Ask-Questions-The-Smart-Way.md)

Happy using USSLAB HPC! 🥳

## 参考链接

网站还在建设中，以下是一些参考链接：
- [中国科大超级计算中心用户使用文档](https://scc.ustc.edu.cn/zlsc/user_doc/html/index.html)
- [武汉大学超算中心用户手册](https://docs.hpc.whu.edu.cn/)
- [Slurm | Documentation](https://slurm.schedmd.com/documentation.html)
- [Google | slurm-users](https://groups.google.com/g/slurm-users)