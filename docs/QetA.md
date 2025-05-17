---
sidebar_position: 4
sidebar_label: '常见问题'
title: 'QA'
---

计划在[google groups](https://groups.google.com/g/usslab-server-maintain)建立一个问题维护，可以看到大家常问的问题。欢迎加入

（当然，在左上GitHub里发[issue](https://github.com/IssacRunmin/usslab-user-doc/issues)也是可以的）

<details>
  <summary>找不到想要的问题/答案？</summary>
请[email](mailto:ourunmin@zju.edu.cn)，在询问管理员问题前看看[这个](https://github.com/ryanhanwu/How-To-Ask-Questions-The-Smart-Way/blob/main/README-zh_CN.md)

- 为什么是email？不是管理员想摆架子，TA也有自己的事情正忙，也许此刻不想被打断
- 如果大家遇到相似的问题都找一次admin，那TA要被烦死了_(:з」∠)_
- 只要表明在问问题前做了一些努力，admin还是很愿意帮助你的！Feel free to ask.
</details>

## Q1: 如何登录到节点，我有些历史数据存放在/home目录中

A: 你可以通过在登录节点moon使用srun来登录到特定节点：
```bash
srun --partition=highend -w node29 --pty bash
```
> `--partition`分区，分为`highend`和`normal`，highend是A100和A800那两个服务器，moon和sun；
>
> `-w node29`是意愿分配在某个节点，node29可以通过ping的输出看到，如`ping sun`；
>
> `--pty bash`是申请pty，即申请终端，使用bash交互操作。

注意这时是没有申请GPU的（大概率不会排队）
等新硬盘到了，容量够的话，会把大家原本/home的数据迁移到/mnt/home下（比如说/mnt/home/[user]/sun）；

## Q2: 如何只用一部分显卡？我想多个任务跑在一张显卡上/与他人共享显卡

A: 你可以使用参数如`--gres=shard:90`来申请90GB的显存：

`srun --partition=highend --gres=shard:90 --pty bash`

```bash
ourunmin@moon:~$ srun --partition=highend --gres=shard:90 --pty bash
ourunmin@sun:~$ nvidia-smi
Thu Oct 17 19:47:19 2024
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 550.54.14              Driver Version: 550.54.14      CUDA Version: 12.4     |
|-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  NVIDIA A800-SXM4-80GB          Off |   00000000:3E:00.0 Off |                    0 |
| N/A   77C    P0            279W /  400W |   41855MiB /  81920MiB |     95%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+
|   1  NVIDIA A800-SXM4-80GB          Off |   00000000:46:00.0 Off |                    0 |
| N/A   28C    P0             42W /  400W |       0MiB /  81920MiB |      0%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+

+-----------------------------------------------------------------------------------------+
| Processes:                                                                              |
|  GPU   GI   CI        PID   Type   Process name                              GPU Memory |
|        ID   ID                                                               Usage      |
|=========================================================================================|
|    0   N/A  N/A    758777      C   ...unmingp/envs/def_measure/bin/python      41846MiB |
+-----------------------------------------------------------------------------------------+
```

你可以使用`sinfo -o "%N %G"` (NodeList & GRES) 来查看每个节点的显卡配置（详细配置使用`scontrol show node`查看)：

```bash
sinfo -o "%P %N %G"
PARTITION NODELIST GRES
highend node29 gpu:a800:4,shard:a800:320
normal* node28 gpu:rtx3090:6,shard:rtx3090:144
<snip>
```

解释：

- 高性能区域(highend)节点`node29`，配备了A800显卡4张，可分配显存320GB，即每张显卡80GB显存
- 普通区域(normal)节点`node28`，配备了RTX3090显卡6张，可分配显存144GB，即每张显卡24GB显存
- normal'*'表示默认提交在这个分区
- 可以申请超过一张显卡的显存用量，如上述例子中`--gres=shard:90`申请了90GB，但A800一张卡只有80GB，这样相当于申请了两张显卡，并且和其他人共享一张（`nvidia-smi`中有他人的任务）。针对上述情况，请使用智能分配策略拆分模型，如`transformers`的`device_map="auto"`:
  > transformers.AutoModelForCausalLM.from_pretrained('xxx', device_map="auto")

注意：

1. SLURM分配后，一张显卡状态是独占(`--gres=gpu:1`) 或者共享(`--gres=shard:10`)其一，申请独占显卡即使没有满显存也不会有其他任务加入
2. 当前SLURM版本(24.05)的`shard:XX(GB)`并不真正限制显存占用，**请合理估算显存，避免申请和实际使用不符**，导致后续任务出错(`RuntimeError: CUDA error: out of memory`)。

## Q3: `/mnt/home`和`/mnt/data`有什么不同？

A1: 两个都是共享文件系统，底层硬盘和机制有所不同：

1. `/mnt/home`为固态硬盘，`/mnt/data`为机械硬盘，存储速度`/mnt/home`更快，适合代码/环境存储（但后续会限制磁盘大小）；`/mnt/data`量大，适合数据集存储。
2. `/mnt/home`使用1:1冗余，1个计算节点掉线不会影响数据读写，但文件在此系统里占两倍大小；`/mnt/data`则无冗余，计算节点掉线可能会导致数据读写卡住（IO操作无响应，程序进入'D'状态）。请注意冗余不是备份，用户删除文件将是彻底删除，无法恢复。服务器数据量大，目前硬盘较少，提供不了备份服务。
