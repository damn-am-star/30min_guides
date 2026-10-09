# Shell 脚本 30 分钟入门

本文面向有编程经验的读者，介绍如何编写、运行和检查常见 Shell 脚本。示例使用 POSIX `sh`，避免依赖某个特定 Shell 的扩展；如果改用 Bash，应在 shebang 中明确写出 Bash，并查阅 Bash 手册。

## 1. Shell 与 Shell 脚本

Shell 是读取命令并与操作系统交互的程序，例如 `sh`、`bash`、`zsh`。Shell 脚本则是写给 Shell 执行的一组命令。终端中交互式输入的命令和脚本遵循许多相同规则。

先确认系统有哪些解释器：

```sh
command -v sh
command -v bash
```

本教程适用于常见的 Unix-like 系统，包括 Linux 和 macOS。Windows 用户可使用 WSL、Git Bash 等兼容环境；环境之间的工具和路径可能不同。

## 2. 编写并运行第一个脚本

创建 `hello.sh`：

```sh
#!/bin/sh

set -eu

name=${1:-world}
printf 'Hello, %s!\n' "$name"
```

第一行称为 shebang，表示直接执行文件时使用哪个解释器。`${1:-world}` 在第一个参数未设置或为空时使用默认值。变量展开通常要加双引号，避免空格和通配符意外拆分参数。

运行方式：

```sh
sh hello.sh "Ada Lovelace"
```

或赋予执行权限后直接运行：

```sh
chmod +x hello.sh
./hello.sh "Ada Lovelace"
```

`./` 表示从当前目录运行。当前目录通常不在 `PATH` 中，所以仅输入 `hello.sh` 通常找不到该文件。

## 3. 变量、参数与引用

赋值时等号两边不能有空格，使用变量时加 `$`：

```sh
greeting='hello'
name='Ada Lovelace'
printf '%s, %s\n' "$greeting" "$name"
```

- 单引号保留其中字符的原样含义，不能直接在单引号中嵌入另一个单引号。
- 双引号允许变量展开；大多数情况下，变量展开应写成 `"$name"`。
- `$1`、`$2` 是位置参数，`"$@"` 表示保留边界的全部参数，`"$*"` 则会将参数合并成一个字符串。
- `$?` 是上一条命令的退出状态；通常 `0` 表示成功，非零表示失败。

示例：逐个处理传入的参数。

```sh
for item do
    printf '参数：%s\n' "$item"
done
```

## 4. 条件判断与循环

Shell 用命令的退出状态判断成功或失败。`if` 后面的命令成功时执行 `then` 分支：

```sh
if [ -f "$1" ]; then
    printf '文件存在\n'
else
    printf '文件不存在\n'
fi
```

`[` 是命令，因此括号前后需要空格；变量应加引号，防止空值或空格导致测试表达式错误。常见文件测试还有 `-d`（目录）、`-r`（可读）和 `-x`（可执行）。

遍历文件名时不要解析 `ls` 输出。使用 Shell 的路径名展开，并启用可能的空匹配保护：

```sh
for file in ./*.txt; do
    [ -e "$file" ] || continue
    printf '%s\n' "$file"
done
```

等待某个文件出现：

```sh
while [ ! -e "$1" ]; do
    sleep 1
done
```

## 5. 函数与退出状态

函数可以组织重复操作，并通过命令的退出状态表示成功或失败：

```sh
print_file() {
    file=$1
    if [ ! -r "$file" ]; then
        printf '无法读取：%s\n' "$file" >&2
        return 1
    fi
    cat "$file"
}

print_file "${1:?用法：sh script.sh FILE}"
```

`return` 传回函数状态；`exit` 结束整个脚本。错误信息通常写到标准错误（`>&2`）。`set -eu` 可帮助尽早发现未定义变量和失败命令，但它并非完整的错误处理方案，尤其要理解 `if`、管道和条件列表中的行为。

## 6. 管道、重定向与常用命令

管道 `|` 把一个命令的标准输出接到另一个命令的标准输入。重定向可保存输出或读取文件：

```sh
grep -n 'error' application.log > errors.txt
command >> output.log 2>&1
```

常用文本工具：

- `grep`：筛选包含指定模式的行。
- `sed`：按规则转换文本。
- `awk`：按行和字段处理结构化文本。
- `find`：按条件查找文件。
- `xargs`：将输入转成命令参数；含有任意文件名时应使用支持 NUL 分隔的选项，或用 `find -exec`。

不要通过 `for f in $(ls ...)` 或 `for f in $(find ...)` 处理文件名：空格、换行和通配符会破坏拆分结果。处理不可信输入时，避免 `eval`，也不要把用户输入拼接成要执行的 Shell 命令。

## 7. 调试与习惯

- 用 `sh -n script.sh` 检查语法。
- 用 `set -x` 临时追踪执行的命令；调试结束后移除，避免敏感值进入日志。
- 给变量加引号，使用 `printf` 输出可预测的格式。
- 对每个外部命令检查失败可能性，并设计清楚的错误处理。
- 把复杂的数据处理交给 Python、Ruby 等更适合的语言；Shell 擅长串接系统工具，不擅长复杂数据结构。
- 阅读目标系统自带的手册页，并确认脚本依赖的命令在运行环境中可用。

## 参考资料

- POSIX Shell Command Language：<https://pubs.opengroup.org/onlinepubs/9799919799/utilities/V3_chap02.html>
- GNU Bash Reference Manual：<https://www.gnu.org/software/bash/manual/>
