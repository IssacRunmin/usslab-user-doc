---
sidebar_position: 5
sidebar_label: '更新日志'
title: '更新日志'
---

## v1.1-pre-release: 2024-10-17 ~ 2024-10-29

### doc-v1.1.3: 2024-10-30

- 集群进阶--远程调试[[modify](./tutorial-extras/2_remote_debug.md)]: 详尽描述远程调试方法

### doc-v1.1.2: 2024-10-29

- 写在前面[add](./intro.md): 增加提问方式
- 常见问题[add](./QetA.md): 增加提问方式

### USSLAB_HPC-doc-v1.1.1: 2024-10-26

- 集群基础--基础知识[[add](./tutorial-basics/0_Basic.md#环境模块module)]: 增加环境模块（module）描述
- 集群基础——基础操作：
  - [[modify](./tutorial-basics/2_Operation.md#批处理提交sbatch)]: 增加模块加载，可以在脚本里使用`conda activate`
  - [[add](./tutorial-basics/2_Operation.md#查看队列squeue)]: 查看队列和排队原因
  - [[add](./tutorial-basics/2_Operation.md#取消作业scancel)]: 取消作业的操作
- 集群进阶--环境管理[[init](./tutorial-extras/3_advance_opt.md)]: 增加module相关命令

### USSLAB_HPC-doc-v1.1.0: 2024-10-17

- 写在前面[init]：注意事项

- 用户手册——基础部分[init]
  - 集群知识——基础[init]：overview和大纲
  - 集群基础——连接[init]：连接信息和连接方法
  - 集群基础——操作[init]：各种基础操作大纲

- 用户手册高级部分[init]
  - 远程调试[init]：vscode远程调试方法
  - 消息通知[init]：消息通知方法

- 常见问题
  - 常见问题 1 [init]：访问服务器/home目录
  - 常见问题 2 [init]：申请显存大小来使用显卡

## 通知日志

### ⚠️更新-2025.05.02-16:00

由于5.1时候`/mnt/data`有一个硬盘（6TB）部分损坏（失联，已修复且未造成数据丢失），`/mnt/data`的数据迁移比预期要久，目前还在迁移。

---

由于`/mnt/home`共享文件系统接近满载，负载均衡没有调节好，导致所有服务器使用的硬盘只是集群的一两个（其他都是满载的；之前都是硬盘满一个增加一个，所以导致读取硬盘集中在新硬盘）。这导致了很大的性能问题：登录节点卡顿，任务排长队，总体gpu util%不高甚至接近0%。

为彻底解决此问题，缓解硬盘瓶颈，计划五一期间对网络文件系统进行数据迁移和升级。2025年4月30日-5月5日，对网络文件系统磁盘整理，开放`/mnt/data`。期间可能会影响到文件的读写操作，请提前做好数据备份。

### 具体安排：

1. 4月30日-5月2日：`/mnt/data`：增加存储空间，迁移各服务器旧数据（服务器未成立集群时候的/home目录）至`/mnt/data/old_home/<server>`中，预计存储增加到110TB。迁移方法：每个目录在复制到目标目录后进行格式化。迁移计划如下（服务器名称 - > 迁移目录 - > 迁移大小）
   - [x] star: /home, 共8TB
   - [x] hermit: /home, 共16TB；
   - [x] (ongoing) strength: /home, 共10TB
   - [x] sun: /mnt/local_home, 共16TB
   - [x] temperance: /home，共425GB
   - [x] moon: /home，共3.5TB
   - [ ] magician: /home, /home/usslab/Documents，共17TB （后续重装系统\*）
   - [ ] fool: /home, /home/usslab/Documents，共17TB（后续重装系统\*）
   - 不迁移：warriors, justice, empress, lover 
   - \*注：视情况进行迁移，可能不足够空间
2. 【重要】5月2日至5月4日：`/mnt/home`迁移至`/mnt/data/home`。逐个用户迁移，迁移期间该用户无法登陆并锁定home目录，在跑任务的小伙伴默认不进行迁移，如果要五一期间跑任务的，请联系管理员迁移数据的时间。
3. 将在节末或节后（具体时间段另行提前通知）进行底层文件系统升级，期间将用户目录切换至`/mnt/data/home`
   - 格式化`/mnt/home`
   - 增加metadata SSD，提升文件系统性能
   - 机械硬盘从`/mnt/home`退出，加入到`/mnt/data`所在的文件系统中

### Hint

后续将限制home目录的Quota，数据集等量大的数据可以放在/mnt/data中。大家可以养成代码/数据分开的习惯，可以用快捷方式(`ln -s <dst> <src>`)的方式来达到同一个逻辑目录下读取数据的目的。
例子：

```bash
# lately, I will create such symlink in each user's home
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