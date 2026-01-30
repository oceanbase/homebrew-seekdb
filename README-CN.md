# SeekDB Homebrew 安装包

本目录包含 OceanBase SeekDB 的 Homebrew 安装配置。

## 目录结构

```
homebrew-seekdb/
├── Formula/
│   └── seekdb.rb              # Homebrew formula
└── README.md                  # 本文档
```

## 安装

### 使用 Homebrew Tap 安装

```bash
# 添加 tap（如果还没有添加）
brew tap oceanbase/seekdb

# 安装 SeekDB
brew install seekdb
```

安装完成后，SeekDB 会自动创建以下目录结构：
- 数据目录: `/opt/homebrew/var/seekdb/data`
- PID 文件: `/opt/homebrew/var/seekdb/run/seekdb.pid`
- 日志文件: `/opt/homebrew/var/seekdb/data/log/seekdb.log`

## 使用方法

### 启动 SeekDB

#### 方式 1: 使用管理命令（推荐）

```bash
seekdb-start
```

这会以后台守护进程方式启动 SeekDB，并自动：
- 检查是否已经运行
- 创建必要的目录
- 写入 PID 文件
- 将日志输出到日志文件

#### 方式 2: 前台模式（用于调试）

```bash
seekdb --nodaemon
```

前台模式首次启动会比较快，适合调试和开发。
还可以自定义数据目录

```bash
seekdb --base-dir=/custom/path
```

### 停止 SeekDB

#### 方式 1: 使用管理命令（推荐）

```bash
seekdb-stop
```

这会：
- 读取 PID 文件
- 使用 `kill -KILL` 强制终止进程
- 清理 PID 文件

### 检查状态

#### 方式 1: 使用管理命令（推荐）

```bash
seekdb-status
```

这会显示：
- 进程是否运行
- PID 信息
- 数据目录、PID 文件、日志文件路径
- 进程详细信息（CPU、内存、运行时间等）

#### 方式 3: 检查进程

```bash
# 检查 PID 文件
cat /opt/homebrew/var/seekdb/run/seekdb.pid

# 或使用 ps
ps aux | grep seekdb
```

### 连接数据库

```bash
# 使用 MySQL 客户端
mysql -h 127.0.0.1 -P 2881 -u root

# 或使用 mycli (更好的命令行体验)
mycli -h 127.0.0.1 -P 2881 -u root
```

## 文件路径

使用 Homebrew 安装时，默认路径如下：

### Apple Silicon Mac (M1/M2/M3)
- 数据目录: `/opt/homebrew/var/seekdb/data`
- PID 文件: `/opt/homebrew/var/seekdb/run/seekdb.pid`
- 日志文件: `/opt/homebrew/var/seekdb/data/log/seekdb.log`
- 二进制文件: `/opt/homebrew/bin/seekdb`
- 管理命令: `/opt/homebrew/bin/seekdb-start`, `/opt/homebrew/bin/seekdb-stop`, `/opt/homebrew/bin/seekdb-status`

### Intel Mac
- 数据目录: `/usr/local/var/seekdb/data`
- PID 文件: `/usr/local/var/seekdb/run/seekdb.pid`
- 日志文件: `/usr/local/var/seekdb/data/log/seekdb.log`
- 二进制文件: `/usr/local/bin/seekdb`
- 管理命令: `/usr/local/bin/seekdb-start`, `/usr/local/bin/seekdb-stop`, `/usr/local/bin/seekdb-status`

## 端口说明

| 端口 | 用途 |
|------|------|
| 2881 | MySQL 协议端口 |

## 依赖项

SeekDB 依赖以下 Homebrew 包：
- `zstd` - 压缩库
- `utf8proc` - UTF-8 处理库
- `thrift` - RPC 框架
- `re2` - 正则表达式库
- `brotli` - 压缩算法

这些依赖会在安装 SeekDB 时自动安装。

## 故障排除

### SeekDB 启动失败

1. **检查端口是否被占用**:
```bash
lsof -i :2881
```

2. **检查日志文件**:
```bash
# 查看日志
tail -f /opt/homebrew/var/seekdb/data/log/seekdb.log

# 或
cat /opt/homebrew/var/seekdb/data/log/seekdb.log
```

3. **检查 PID 文件**:
```bash
# 如果 PID 文件存在但进程不存在，可能是残留文件
cat /opt/homebrew/var/seekdb/run/seekdb.pid

# 清理残留 PID 文件
rm /opt/homebrew/var/seekdb/run/seekdb.pid
```

4. **检查磁盘空间**:
```bash
df -h
```

### 权限问题

```bash
# 确保数据目录有正确权限
chmod -R 755 /opt/homebrew/var/seekdb
```

### macOS 安全设置

如果 macOS 阻止应用运行：
1. 打开"系统偏好设置" > "安全性与隐私"
2. 点击"仍要打开"或"允许"来允许 SeekDB 运行

### 内存不足

SeekDB 最低需要 2GB 内存。检查系统内存:
```bash
sysctl hw.memsize
```

### 后台启动较慢

首次后台启动可能需要约 10 秒，这是正常的 macOS 线程优先级优化行为。如果需要快速启动，可以使用前台模式：
```bash
seekdb --nodaemon
```

## 卸载

```bash
# 停止服务
seekdb-stop

# 卸载 SeekDB
brew uninstall seekdb

# 删除数据目录（可选，会删除所有数据）
rm -rf /opt/homebrew/var/seekdb
```

## 版本信息

- 当前版本: 1.0.0
- Homepage: https://github.com/oceanbase/seekdb
- License: Apache-2.0

## 相关链接

- [OceanBase SeekDB](https://github.com/oceanbase/seekdb)
- [Homebrew Formula Cookbook](https://docs.brew.sh/Formula-Cookbook)
