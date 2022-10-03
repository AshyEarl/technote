<!-- vscode-markdown-toc -->
* 1. [#!/bin/bash [参见这里](https://tldp.org/LDP/abs/html/sha-bang.html)](#binbashhttps:tldp.orgLDPabshtmlsha-bang.html)
* 2. [shell中定义和使用方法](#shell)
* 3. [shell中的局部变量，[参见这里](https://bash.cyberciti.biz/guide/Local_variable)](#shellhttps:bash.cyberciti.bizguideLocal_variable)
* 4. [2>/dev/null, [参见这里](https://askubuntu.com/questions/350208/what-does-2-dev-null-mean)](#devnullhttps:askubuntu.comquestions350208what-does-2-dev-null-mean)
* 5. [文件相关](#)
* 6. [特殊符号](#-1)
* 7. [命令行参数](#-1)

<!-- vscode-markdown-toc-config
	numbering=true
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc -->[参见这里](https://tldp.org/LDP/abs/html/)



##  1. <a name='binbashhttps:tldp.orgLDPabshtmlsha-bang.html'></a>#!/bin/bash [参见这里](https://tldp.org/LDP/abs/html/sha-bang.html)
`#!`用于标记后续的命令使用什么工具来执行,通常在linux中是：`#!/bin/bash`
```bash
#!/bin/rm
# 自删除的脚本，下面也不会输出
echo "This line never print"
```
```bash
#!/bin/more

# more 工具用于将文本自动折行显示在屏幕上，给ReadMe添加可以用于浏览
xxxxx
```

##  2. <a name='shell'></a>shell中定义和使用方法
```bash
# 定义方法
function test(){
    echo "This is msg from function test."
}

# 调用方法
test
```

##  3. <a name='shellhttps:bash.cyberciti.bizguideLocal_variable'></a>shell中的局部变量，[参见这里](https://bash.cyberciti.biz/guide/Local_variable)
shell中默认变量操作是全局的，只有在方法中添加local关键字才是局部的
```bash
# 变量声明等号左右不能有空格，否则不是变量声明，参见 https://tldp.org/LDP/abs/html/varsubn.html
var="This is default value"
function name(){
    local var=$1
    echo "local var is ${var}"
}

# 直接引用会自动去掉里面的空格
hello="A B  C   D"
# A B C D
echo $hello
# A B  C   D
echo "$hello"

# 值中有空格必须使用引号，或者用反斜杠
numbers="1 2 3"
numbers=1\ 2\ 3

# 使用let关键字
let number=16+5
let "number = 16 + 5"
let "number += 12"
let "number = $number * 3"
```

##  4. <a name='devnullhttps:askubuntu.comquestions350208what-does-2-dev-null-mean'></a>2>/dev/null, [参见这里](https://askubuntu.com/questions/350208/what-does-2-dev-null-mean)
`>`用于将`输出流`重定向到文件或者设备，这里的`/dev/null`是一个特殊设备文件，它会将输入的数据直接抛弃，`>`前不指定数字默认为标准输出流(stdout),指定2为标准错误流(stderr)
- `> file`: 标准输出流(stdout)重定向到 file
- `1> file`: 标准输出流(stdout)重定向到 file
- `2> file`: 标准错误流(stderr)重定向到 file
- `&> file`: 标准输出流(stdout)和错误流(stderr)都重定向到 file
- `> file 2>&1`: 标准输出流(stdout)和错误流(stderr)都重定向到 file

##  5. <a name=''></a>文件相关
- `-e`: 文件存在
- `-a`: 文件存在（被弃用）
- `-f`: 是普通文件（非文件夹或者设备文件）
- `-d`: 是文件夹
- `-b`: block device
- `-c`: character device
- `-p`: pipe
- `-h`,`-L`: 链接文件
- `-S`: socket
- `-t`: terminal device
- `-w`: 有写入权限
- `-x`: 有执行权限
- `-O`: 文件的Ower
- `f1 -nt f2`: f1比f2新
- `f1 -ot f2`: f2比f2旧
- `f1 -ef f2`: f1,f2硬链接到了同一个文件

##  6. <a name='-1'></a>特殊符号
- `#`: 注释，必须在其前面添加一个空格
  ```bash
  # 下面这个注释不正常
  echo hello# Can't be comment
  # 下面这个正常
  echo hello # This is ok
  ```
- `;`: 用于一行中多条命令
  ```bash
  echo hello; echo world
  ```
- `;;`: 用于`case`中的单项
  ```bash
  case $msg in
    abc) echo msg is abc;;
    efg) echo msg is efg;;
  esac
  ```
- `,`: 逗号分隔符
  ```bash
  # ','可以用于拼接多条命令，但仅有最后一条的结果会返回，也就是 t2=5
  let "t2 = ((a = 9, 15 / 3))"
  # 也可以用于拼接字符串, 下面会打印出
  # /bin/ipcalc
  # /usr/bin/kcalc
  # /usr/bin/oidcalc
  # /usr/bin/oocalc
  for f in /{,usr/}bin/*calc
  do
    echo $f
  done
  ```
- `: 用于将其内的命令执行的结果返回给变量
  ```bash
  local test=`basename $0`
  echo script is $test
  ```
- `:` 冒号：空命令，默认返回值是true
  ```bash
  # 等同于
  # while true
  # do
  #   xxx
  # done
  while :
  do
    xxx
  done
  ```
- `?`: 三目运算符
  ```bash
  ((test=$1>10?1:0))
  echo "input is bigger than 10: $test"
  ```
- `$`: 变量引用
- `$$`: 当前进程ID
- `()`: 命令组及数组初始化
  ```bash
  # 在()中的命令会在子进程中执行，变量进程见不可见
  val=123
  (val=321)
  # val=123
  echo val=$val

  # 数组初始化
  arr=(a b c)
  ```
- `{aa,bb,cc}`: 用于展开
  ```bash
  # {}中的元素不能有空格，并用,分割
  echo \"{hello,world}\"

  # 等同于： cp file.txt file.backup
  cp file.{txt,backup}
  ```
- `{a..z}`: 范围展开
  ```bash
  # a b c d e f g h i j k l m n o p q r s t u v w x y z
  echo {a..z}
  # 
  base64_charset=( {A..Z} {a..z} {0..9} + / = )
  ```
- `{}`: 类似于匿名方法，代码块
  ```bash
  file=/etc/fstab

  {
    read line1
    read line2
  } < $file

  echo "line 1: $line1"
  echo "line 2: $line2"

  {
    echo "line1: $line1"
    echo "line2 haha: $line2"
  } > out.txt
  ```
- `[]`: test
  ```bash
  # 中间的判断条件两边必须有空格
  if [ true ]; then
    echo "hello"
  fi

  # 也可以用于数组元素的访问
  arr=({a..z})
  # array[1] is b
  echo "array[1] is ${arr[1]}"
  ```
- `[[ ]]`: test, shell的关键字
- `||`,`&&`: 逻辑或，逻辑与
  ```bash
  if [[ -n $0 || -n $1 ]]; then
    echo "params: $0, $1"
  fi
  ```
- `+`,`-`,`*`,`/`,`%`: 算数运算
- `~+`: 当前工作目录，等同于`PWD`变量
- `~-`: 前一个工作目录，等同于`OLDPWD`变量
##  7. <a name='-1'></a>命令行参数
```bash
# $@ 和 $* 都可以代表所有的命令行参数
echo "all args: $*"
echo "all args: $@"
# $0 是脚本本身的文件路径
echo "first arg: $0"
# 第一个参数是 $1
echo "passed first arg: $1"
# 第10个开始需要用{}
echo "passed 10 arg: ${10}"

# $# 用于代表命令行参数的个数, 该个数不包含 $0
echo "arg count: $#"

# 用于移动参数，$2 -> $1
shift 1
```