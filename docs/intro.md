---
sidebar_position: 1
sidebar_label: '写在前面'
title: '写在前面'
---

这是一些使用USSLAB计算集群的**注意事项**

## 近期网络文件系统磁盘整理

### ⚠️更新-2025.05.17

原始home目录迁移完成（`/mnt/data/old_home/<server>`），近期对/mnt/home进行数据迁移并升级，即逐个用户迁移，迁移期间该用户无法登陆并锁定home目录，近期有在登录/在跑任务的小伙伴默认不进行迁移。

（旧通知移步[日志](./log.md)）


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