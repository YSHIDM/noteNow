# noteNow

## 说明

名字挺闹，但一点不孬！

### 功能

1. 使用简单的命令行快速记录笔记
2. 纯粹的 shell 脚本
3. 无需安装
4. 无需 Java
5. 无需 Python
6. 按需配置默认笔记名称，或修改源码
7. 可备份，可归档，可查询
8. note -h 获取帮助信息
9. note -i 获取创建链接、自定义配置等信息

### 使用场景

1. 忘记装了哪些软件
2. 忘记软件安装位置
3. 忘记软件配置位置
4. 忘记软件启动方式
5. 忘记计划进展
6. 忘记常用命令
7. 还有什么来着
8. 不要保存密码

## 需求

写一个名为note 的shell脚本

1. 执行./note -f note1.txt -c 123
    在 -f 指定的文件 note1.txt 中添加 -c 内容：年月日时分秒 >| 123
    没有 -f 时默认保存内容到 note.txt
2. 执行./note -s [content]
    等于 ./note -f secret.txt -c [content]
3. 执行./note [content]
    在note.txt 文件中保存；年月日时分秒 >| 123
4. 执行./note
    在note.txt 文件中保存日期+空：年月日时分秒 >|
5. 执行./note -v
    显示 note.txt 全部内容
6. 执行./note -r 8 -l 4
    显示 note.txt 第 5 至第 8 行，可以搭配 -f
7. 执行./note -l [n]
    显示 note.txt 最后n行，如：./note -l 10，显示 note.txt 最后10行
8. 执行./note -b
    复制 -f 指定文件，新文件名为 backup-时间.txt
9. 执行./note -n
    修改 -f 指定文件，新文件名为 archive-时间.txt
10. 执行./note -g [content]
    搜索 -f 指定文件，使用命令grep 搜索，使用参数 -inE
11. 执行./note -i
    给note文件创建链接命令参考
12. 执行./note -a
    列出指定文件夹下的所有笔记
13. 执行./note -d [n|b|a]
    指定文件夹，a:archive, b:backup, n:notes，默认 notes
14. 执行./note -m [content]
    批量搜索指定文件夹下所有文件，根据搜索条件列出文件名和匹配行
15. 执行./note -h
    显示以上内容

## 测试

```shell {.line-numbers}
note
note 1111
note -s 1111
note -f note1.txt -c 123
# 添加空行
note -f note1.txt
note -v
note -r 6 -l 4
note -l 4
note -f secret.txt -r 3 -l 2
note -b
note -f secret.txt -b
note -n
note -f secret.txt -n
note -g 123
note -f note1.txt -g 123
note -i
note -a
note -d a -a
note -d a -g
note -d a -m wwe
# 已禁用 -d + -c
note -d a -f note2.txt -c 123
# 已禁用 -d + -s
note -d a -s 123
note -d a -f archive_xxx.txt -g 123
note -m 123
note -d a -m 123 
```

## 其他

1. 获取路径

    ```shell {.line-numbers}
    #!/bin/bash
    echo '$0: '$0
    echo "pwd: "`pwd`
    echo "============================="
    echo "scriptPath1: "$(cd `dirname $0`; pwd)
    echo "scriptPath2: "$(pwd)
    echo "scriptPath3: "$(dirname $(readlink -f $0))
    echo "scriptPath4: "$(cd $(dirname ${BASH_SOURCE:-$0});pwd)
    echo -n "scriptPath5: " && dirname $(readlink -f ${BASH_SOURCE[0]})
    ```

2. 简易版

    ```shell {.line-numbers}
    #!/bin/bash
    blank_line=""
    # 检查参数是否存在
    if [ -z "$1" ]; then
        blank_line="空行"
    fi

    # 获取当前日期和时间
    current_date=$(date '+%Y-%m-%d %H:%M')

    # 将参数和日期写入note.txt文件
    echo "$current_date >| $1" >> note.txt

    echo "已保存"$blank_line
    ```

## 配置文件知识

### 参考

1. [linux中环境变量及环境变量配置文件详解](https://www.cnblogs.com/kelelipeng/p/13638185.html)
2. [Linux环境变量配置全攻略](https://www.cnblogs.com/youyoui/p/10680329.html)

### 常见的环境变量

/etc/profile
/etc/profile.d/*.sh
~/.bash_profile
~/.bashrc
/etc/bashrc

用户级别环境变量定义文件：~/.bashrc、~/.profile（部分系统为：~/.bash_profile）
系统级别环境变量定义文件：/etc/bashrc、/etc/profile(部分系统为：/etc/bash_profile）、/etc/environment

### 系统正常启动过程的环境变量的启动

/etc/profile
/etc/profile.d/*.sh
~/.bash_profile
~/.bashrc
/etc/bashrc
命令提示符

### 在其他用户下切换用户的过程

/etc/bashrc
/etc/profile.d/*.sh
命令提示符

### Linux环境变量配置

系统：Ubuntu 14.0

1. export PATH
    * 生效时间：立即生效
    * 生效期限：当前终端有效，窗口关闭后无效
    * 生效范围：仅对当前用户有效
    * 配置的环境变量中不要忘了加上原来的配置，即$PATH部分，避免覆盖原来配置
2. vim ~/.bashrc
    * 生效时间：使用相同的用户打开新的终端时生效，或者手动source ~/.bashrc生效
    * 生效期限：永久有效
    * 生效范围：仅对当前用户有效
    * 如果有后续的环境变量加载文件覆盖了PATH定义，则可能不生效
3. vim ~/.bash_profile
    * 生效时间：使用相同的用户打开新的终端时生效，或者手动source ~/.bash_profile生效
    * 生效期限：永久有效
    * 生效范围：仅对当前用户有效
    * 如果没有~/.bash_profile文件，则可以编辑~/.profile文件或者新建一个
4. vim /etc/bashrc
    * 生效时间：新开终端生效，或者手动source /etc/bashrc生效
    * 生效期限：永久有效
    * 生效范围：对所有用户有效
5. vim /etc/profile
    * 生效时间：新开终端生效，或者手动source /etc/profile生效
    * 生效期限：永久有效
    * 生效范围：对所有用户有效
6. vim /etc/environment
    * 生效时间：新开终端生效，或者手动source /etc/environment生效
    * 生效期限：永久有效
    * 生效范围：对所有用户有效

#### 表格形式

| 序号 | 文件             | 生效时间               | 生效期限 | 生效范围 | 其他                                           |
| ---- | ---------------- | ---------------------- | -------- | -------- | ---------------------------------------------- |
| 1    | export PATH      | 立即生效               | 当前终端 | 当前用户 | 加上$PATH，避免覆盖原来配置                    |
| 2    | ~/.bashrc        | 相同用户新终端, source | 永久     | 当前用户 | PATH定义可能被覆盖                             |
| 3    | ~/.profile       | 相同用户新终端, source | 永久     | 当前用户 | 部分系统为：~/.bash_profile, 不存在则手动创建 |
| 4    | /etc/bashrc      | 新终端, source         | 永久     | 所有用户 |                                                |
| 5    | /etc/profile     | 新终端, source         | 永久     | 所有用户 |/etc/bash_profile|
| 6    | /etc/environment | 新终端, source         | 永久     | 所有用户 |                                                |
