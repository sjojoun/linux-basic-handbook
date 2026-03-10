# Linux 基础操作手册

> 作者：JOJO  
> 最后更新：2026 年 3 月 10 日  
> 版本：1.0

---

## 目录

1. [文件和目录操作](#1-文件和目录操作)
2. [文件内容查看](#2-文件内容查看)
3. [文件和目录权限](#3-文件和目录权限)
4. [用户和组管理](#4-用户和组管理)
5. [进程管理](#5-进程管理)
6. [网络操作](#6-网络操作)
7. [磁盘管理](#7-磁盘管理)
8. [软件包管理](#8-软件包管理)
9. [系统信息](#9-系统信息)
10. [压缩和解压缩](#10-压缩和解压缩)
11. [文本处理](#11-文本处理)
12. [SSH 远程连接](#12-ssh-远程连接)
13. [定时任务](#13-定时任务)
14. [系统服务管理](#14-系统服务管理)
15. [常用快捷键](#15-常用快捷键)

---

## 1. 文件和目录操作

### 基本导航

```bash
# 显示当前工作目录
pwd

# 列出目录内容
ls                    # 基本列表
ls -l                 # 详细列表
ls -a                 # 显示隐藏文件
ls -lh                # 人类可读的文件大小
ls -R                 # 递归列出子目录

# 切换目录
cd /path/to/dir       # 切换到指定目录
cd ..                 # 返回上级目录
cd ~                  # 返回家目录
cd -                  # 返回上一个目录

# 创建目录
mkdir dirname         # 创建单个目录
mkdir -p a/b/c        # 递归创建多级目录
```

### 文件操作

```bash
# 创建文件
touch filename        # 创建空文件或更新文件时间戳
echo "content" > file # 创建文件并写入内容

# 复制文件/目录
cp source dest        # 复制文件
cp -r src_dir dst_dir # 递归复制目录
cp -i source dest     # 覆盖前提示确认

# 移动/重命名文件
mv source dest        # 移动或重命名
mv old.txt new.txt    # 重命名文件

# 删除文件/目录
rm file               # 删除文件
rm -f file            # 强制删除不提示
rm -r directory       # 递归删除目录
rm -rf directory      # 强制递归删除（危险！）

# 查找文件
find /path -name "filename"           # 按名称查找
find /path -type f -name "*.txt"      # 按类型和扩展名查找
find /path -size +100M                # 查找大于 100M 的文件
find /path -mtime -7                  # 查找 7 天内修改的文件

# 定位命令
which command         # 显示命令的完整路径
whereis command       # 显示命令的二进制、源码和手册位置
locate filename       # 快速查找文件（需要 updatedb）
```

### 链接文件

```bash
# 创建软链接（符号链接）
ln -s target link_name

# 创建硬链接
ln target link_name
```

---

## 2. 文件内容查看

```bash
# 查看文件内容
cat filename          # 显示整个文件内容
cat -n filename       # 显示行号

# 分页查看
less filename         # 分页查看（推荐）
more filename         # 分页查看（功能较少）

# 查看文件头部/尾部
head filename         # 查看前 10 行
head -n 20 filename   # 查看前 20 行
tail filename         # 查看后 10 行
tail -n 20 filename   # 查看后 20 行
tail -f filename      # 实时跟踪文件更新（日志监控）
tail -F filename      # 跟踪文件，即使被轮转

# 查看文件行数
wc -l filename        # 统计行数
wc -w filename        # 统计单词数
wc -c filename        # 统计字节数
wc -m filename        # 统计字符数
```

---

## 3. 文件和目录权限

### 查看权限

```bash
ls -l                 # 显示权限信息
```

### 修改权限

```bash
chmod u+x file        # 给所有者添加执行权限
chmod 755 file        # rwxr-xr-x
chmod 644 file        # rw-r--r--
```

### 修改所有者和组

```bash
chown user file       # 修改文件所有者
chown user:group file # 修改所有者和组
chgrp group file      # 修改文件所属组
```

---

## 4. 用户和组管理

```bash
id                    # 显示当前用户信息
whoami                # 显示当前用户名
useradd username      # 创建用户
userdel username      # 删除用户
passwd username       # 修改用户密码
groups                # 显示当前用户的组
groupadd groupname    # 创建组
```

---

## 5. 进程管理

```bash
ps aux                # 显示所有进程
top                   # 实时进程监控
kill PID              # 终止进程
kill -9 PID           # 强制终止
killall process_name  # 按名称终止进程
free -h               # 查看内存
df -h                 # 查看磁盘空间
```

---

## 6. 网络操作

```bash
ip addr               # 查看 IP 地址
ip route              # 查看路由表
ping hostname         # 测试连接
netstat -tuln         # 查看监听端口
ss -tuln              # 查看监听端口
wget url              # 下载文件
curl url              # 发送 HTTP 请求
```

---

## 7. 磁盘管理

```bash
df -h                 # 查看磁盘使用
lsblk                 # 列出块设备
mount                 # 查看已挂载
mount /dev/sdX /mnt   # 挂载设备
umount /mnt           # 卸载设备
```

---

## 8. 软件包管理

### Debian/Ubuntu (apt)

```bash
sudo apt update       # 更新软件包列表
sudo apt upgrade      # 升级软件包
sudo apt install pkg  # 安装软件包
sudo apt remove pkg   # 删除软件包
```

### RHEL/CentOS/Fedora

```bash
sudo dnf update
sudo dnf install pkg
sudo dnf remove pkg
```

### Arch Linux

```bash
sudo pacman -Syu
sudo pacman -S pkg
sudo pacman -R pkg
```

---

## 9. 系统信息

```bash
uname -a              # 系统信息
hostname              # 主机名
uptime                # 运行时间
lscpu                 # CPU 信息
free -h               # 内存信息
lsblk                 # 块设备
```

### 系统日志

```bash
journalctl            # 查看所有日志
journalctl -f         # 实时查看日志
journalctl -u svc     # 查看服务日志
```

---

## 10. 压缩和解压缩

```bash
tar -czvf a.tar.gz f  # gzip 压缩
tar -xzvf a.tar.gz    # 解压 gzip
zip a.zip f           # zip 压缩
unzip a.zip           # 解压 zip
```

---

## 11. 文本处理

```bash
grep "pat" file       # 搜索
sed 's/o/n/g' f       # 替换
awk '{print $1}' f    # 打印列
sort file             # 排序
uniq file             # 去重
cut -d: -f1 f         # 切割字段
```

---

## 12. SSH 远程连接

```bash
ssh user@host         # SSH 登录
ssh -p 2222 u@h       # 指定端口
ssh -i key u@h        # 使用密钥
ssh-keygen -t rsa     # 生成密钥
ssh-copy-id u@h       # 复制公钥
scp f u@h:/p          # 上传文件
scp u@h:/f .          # 下载文件
```

---

## 13. 定时任务

```bash
crontab -e            # 编辑 cron
crontab -l            # 列出 cron
# 每天凌晨 2 点执行
0 2 * * * /path/script.sh
# 每 5 分钟执行
*/5 * * * * /path/script.sh
```

---

## 14. 系统服务管理

```bash
systemctl start svc   # 启动服务
systemctl stop svc    # 停止服务
systemctl restart svc # 重启服务
systemctl status svc  # 查看状态
systemctl enable svc  # 开机启动
systemctl list-timers # 列出定时器
```

---

## 15. 常用快捷键

```bash
Ctrl+C          # 终止命令
Ctrl+Z          # 暂停命令
Ctrl+D          # 退出 shell
Ctrl+L          # 清屏
Ctrl+A          # 行首
Ctrl+E          # 行尾
Ctrl+U          # 删除到行首
Ctrl+R          # 搜索历史
Tab             # 自动补全
```

---

## 附录：常用命令速查表

| 功能 | 命令 |
|------|------|
| 我是谁 | whoami |
| 我在哪 | pwd |
| 这里有什么 | ls -la |
| 创建目录 | mkdir -p path |
| 删除文件 | rm -f file |
| 复制文件 | cp -r src dst |
| 移动文件 | mv src dst |
| 查看文件 | cat file |
| 编辑文件 | vim file |
| 查找文件 | find / -name f |
| 查找内容 | grep text f |
| 查看进程 | ps aux |
| 杀死进程 | kill -9 PID |
| 查看内存 | free -h |
| 查看磁盘 | df -h |
| 查看网络 | ip addr |
| 下载文件 | wget url |
| 压缩文件 | tar -czvf a.tar.gz f |
| 解压文件 | tar -xzvf a.tar.gz |
| 安装软件 | apt install pkg |
| 更新系统 | apt update && apt upgrade |
| 查看日志 | journalctl -f |
| 重启系统 | reboot |
| 关机 | shutdown -h now |

---

## 安全提示

⚠️ **重要安全建议：**

1. 不要随意使用 rm -rf /
2. 谨慎使用 chmod 777
3. 定期更新系统
4. 使用 sudo 而非 root
5. 备份重要数据
6. 使用 SSH 密钥
7. 配置防火墙
8. 查看日志

---

## 学习资源

- Linux 命令行与 shell 脚本编程大全
- 鸟哥的 Linux 私房菜
- Linux Man Pages
- Explain Shell
- TLDR Pages

---

**祝你在 Linux 的世界里探索愉快！** 🐧

*作者：JOJO*
