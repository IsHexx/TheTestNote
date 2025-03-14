# Shell 常用脚本

## 一、Shell 基础案例

### 1.1 HelloWorld
```bash
#!/bin/bash

function example {
    echo "Hello world!"
}
example
```

### 1.2 打印运行的 Python 进程
```bash
#!/bin/sh
pidlist=`ps -aux | grep python | awk '{print $2}'`
echo $pidlist
```

### 1.3 获取并打印参数
```bash
#!/bin/bash
echo "$0 $1 $2 $3"  # 传入三个参数
echo $#    # 获取传入参数的数量
echo $@    # 打印获取传入参数
echo $*    # 打印获取传入参数
```

### 1.4 用脚本写 for 循环
```bash
#!/bin/bash
s=0;
for((i=1;i<100;i++))
do 
    s=$[$s+$i]
done 

echo $s

r=0;
a=0;
b=0;
for((x=1;x<9;x++))
do 
    a=$[$a+$x] 
    echo $x
done
for((y=1;y<9;y++))
do 
    b=$[$b+$y]
    echo $y
done

echo $r=$[$a+$b]
```

### 1.5 使用 C 语言风格的 for 命令
```bash
#!/bin/bash
# testing the C-style for loop
for (( i=1; i<=10; i++ ))
do
    echo "The next number is $i"
done
```

### 1.6 while 循环案例
```bash
#!/bin/bash
s=0
i=1
while [ $i -le 100 ]
do
    s=$[$s + $i]
    i=$[$i + 1]
done

echo $s
echo $i
```

### 1.7 使用 break 跳出外部循环
```bash
#!/bin/bash
# break n，默认为1
for (( a=1; a<=3; a++ ))
do
    echo "Outer loop : $a"
    for (( b=1; b < 100; b++ ))
    do 
        if [ $b -gt 4 ]
        then
            break 2
        fi
        echo " Inner loop:$b"
    done
done
```

### 1.8 使用 continue 命令
```bash
#!/bin/bash
# using the continue command
for (( var1 = 1; var1 < 15; var1++ ))
do
    if [ $var1 -gt 5 ] && [ $var1 -lt 10 ]
    then
        continue
    fi
    echo "Iteration number:$var1"
done
```

### 1.9 case 案例
```bash
#!/bin/bash 
case $1 in 
1) 
    echo "wenmin "
    ;;
2)
    echo "wenxing "
    ;; 
3)  
    echo "wemchang "
    ;;
4) 
    echo "yijun"
    ;;
5)
    echo "sinian"
    ;;
6)  
    echo "sikeng"
    ;;
7) 
    echo "yanna"
    ;;
*)
    echo "danlian"
    ;; 
esac
```

### 1.10 判断两个数是否相等
```bash
num1=100
num2=100
if test $[num1] -eq $[num2]
then
    echo '两个数相等！'
else
    echo '两个数不相等！'
fi
```

### 1.11 使用双圆括号
```bash
#!/bin/bash
# using double parenthesis
var1=10
if (( $var1 ** 2 > 90 ))
then 
    (( var2 = $var1 ** 2 ))
    echo "The square of $var1 is $var2"
fi
```

### 1.12 使用双方括号
```bash
#!/bin/bash
# using pattern matching
if [[ $USER == r* ]]
then 
    echo "Hello $USER"
else
    echo "Sorry, I do not know you"
fi
```

### 1.13 反引号的使用
```bash
#!/bin/bash
# using the backtick character
testing=`date`
echo "The date and time are: $testing"
```

### 1.14 字符串比较
```bash
#!/bin/bash
# testing string equality
testuser=tiandi
if [ $USER = $testuser ]
then
    echo "Welcome $testuser"
fi
```

### 1.15 读取列表中的值
```bash
#!/bin/bash
# basic for command
for test in Alabama Alaska Arizona
do
    echo "The next state is $test"
done
```

### 1.16 打印 99 乘法表
```bash
#!/bin/bash
for i in `seq 9`
do 
    for j in `seq $i`
    do 
        echo -n "$j*$i=$[i*j] "
    done
    echo
done
```

### 1.17 脚本编写：求和与函数运算
```bash
#!/bin/bash
function sum {
    s=0
    s=$[$1+$2]
    echo $s
}
read -p "input your parameter " p1
read -p "input your parameter " p2
sum $p1 $p2

function multi {
    r=0
    r=$[$1/$2]
    echo $r
}
read -p "input your parameter " x1
read -p "input your parameter " x2
multi $x1 $x2

v1=1
v2=2
let v3=$v1+$v2
echo $v3
```

### 1.18 用户猜数字
```bash
#!/bin/bash
# 脚本生成一个 100 以内的随机数，提示用户猜数字，根据用户的输入，提示用户猜对了、猜小了或猜大了，直至用户猜对脚本结束。
num=$[RANDOM%100+1]
echo "$num"

while :
do 
    read -p "计算机生成了一个 1-100 的随机数，你猜: " cai  
    if [ $cai -eq $num ]   
    then     
        echo "恭喜，猜对了"     
        exit  
    elif [ $cai -gt $num ]  
    then       
        echo "Oops，猜大了"    
    else      
        echo "Oops，猜小了" 
    fi
done
```

### 1.19 编写剪刀、石头、布游戏
```bash
#!/bin/bash
game=(石头 剪刀 布)
num=$[RANDOM%3]
computer=${game[$num]}

echo "请根据下列提示选择您的出拳手势"
echo " 1. 石头"
echo " 2. 剪刀"
echo " 3. 布 "

read -p "请选择 1-3 ：" person
case $person in
1)
    if [ $num -eq 0 ]
    then 
        echo "平局"
    elif [ $num -eq 1 ]
    then
        echo "你赢"
    else 
        echo "计算机赢"
    fi
    ;;
2)
    if [ $num -eq 0 ]
    then
        echo "计算机赢"
    elif [ $num -eq 1 ] 
    then
        echo "平局"
    else 
        echo "你赢"
    fi
    ;;
3)
    if [ $num -eq 0 ]
    then  
        echo "你赢"
    elif [ $num -eq 1 ]
    then 
        echo "计算机赢"
    else 
        echo "平局"
    fi
    ;;
*)
    echo "必须输入1-3 的数字"
    ;;
esac
```

### 1.20 检测当前用户是否为管理员
```bash
#!/bin/bash
# 检测本机当前用户是否为超级管理员
if [ $USER == "root" ]
then
    echo "您是管理员，有权限安装软件"
else
    echo "您不是管理员，没有权限安装软件"
fi
```

### 1.21 接收参数
```bash
#!/bin/bash -xv
if [ $1 -eq 2 ] ;then
    echo "wo ai wenmin"
elif [ $1 -eq 3 ] ;then
    echo "wo ai wenxing "
elif [ $1 -eq 4 ] ;then
    echo "wo de xin "
elif [ $1 -eq 5 ] ;then
    echo "wo de ai "
fi
```

### 1.22 读取控制台传入的参数
```bash
#!/bin/bash
read -t 7 -p "input your name " NAME
echo $NAME

read -t 11 -p "input your age " AGE
echo $AGE

read -t 15 -p "input your friend " FRIEND
echo $FRIEND

read -t 16 -p "input your love " LOVE
echo $LOVE
```

### 1.23 获取用户输入
```bash
#!/bin/bash
# testing the reading command
echo -n "Enter your name:"
read name
echo "Hello $name, welcome to my program"

read -p "Please enter your age: " age
days=$[ $age * 365 ]
echo "That makes you over $days days old"

# 指定多个变量，输入的每个数据值都会分配给表中的下一个变量，如果用完了，就全分配给最后一个变量
read -p "Please enter name:" first last
echo "Checking data for $last. $first..."

# 如果不指定变量，read 命令就会把它收到的任何数据都放到特殊环境变量 REPLY 中
read -p "Enter a number:"
factorial=1
for (( count=1; count<=$REPLY; count++ ))
do
    factorial=$[ $factorial * $count ]
done
echo "The factorial of $REPLY is $factorial"
```

### 1.24 根据计算机当前时间，返回问候语
```bash
#!/bin/bash
# 根据计算机当前时间，返回问候语，可以将该脚本设置为开机启动
tm=$(date +%H)
if [ $tm -le 12 ];then
    msg="Good Morning $USER"
elif [ $tm -gt 12 -a $tm -le 18 ];then
    msg="Good Afternoon $USER"
else
    msg="Good Night $USER"
fi
echo "当前时间是:$(date +"%Y-%m-%d %H:%M:%S")"
echo -e "\033[34m$msg\033[0m"
```

