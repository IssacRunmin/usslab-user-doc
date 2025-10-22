---
sidebar_position: 1
sidebar_label: '写在前面'
title: '写在前面'
---

这是一些使用USSLAB计算集群的**注意事项**

（旧通知移步[日志](./log.md#通知日志)）


## 用前需知

- 需要有基本的 Linux/Unix 系统使用经验，会进行基础的Linux操作
- 请先在自己的电脑上将计算程序编写调试正确，再到超算上提交运行
- 服务器暂无数据备份，请勿在超算存储长期存放数据，确保计算结果等重要数据至本地保存
- 误删除、误覆盖的文件无法恢复
- 有问题请email: ourunmin@zju.edu.cn Before asking, refer to [this](https://github.com/selfteaching/How-To-Ask-Questions-The-Smart-Way/blob/master/How-To-Ask-Questions-The-Smart-Way.md)

Happy using USSLAB HPC! 🥳

## 建设脚步

- [ ] 文件系统提速（DUE: 2025.11.1）：`/mnt/home`升级，提升I/O性能，解决I/O异常问题
- [ ] 增加GPU服务器（DUE: 2025.10.26）：新增多台GPU服务器，缓解排队问题
- [ ] 增加存储空间（DUE: 2025.11.20）：新增存储服务器，扩展多种存储方式
- [ ] 网站完善（DUE: 2025.12.01）：完善网站内容，增加更多教程和常见问题
    - [ ] DNS域名解析（DUE: 2025.11.01）：绑定域名，方便访问
    - [ ] VScode Web Server（DUE: 2025.12.01）：提供VScode Web Server服务
- [ ] 自动化监控（DUE: 2026.01.01）：实现自动化监控和报警，提升系统稳定性

## 参考链接

网站还在建设中，以下是一些参考链接：
- [中国科大超级计算中心用户使用文档](https://scc.ustc.edu.cn/zlsc/user_doc/html/index.html)
- [武汉大学超算中心用户手册](https://docs.hpc.whu.edu.cn/)
- [Slurm | Documentation](https://slurm.schedmd.com/documentation.html)
- [Google | slurm-users](https://groups.google.com/g/slurm-users)