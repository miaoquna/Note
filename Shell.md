# Shell 教程 #

- [1. Shell变量](#1-shell变量)
	- [1.1. 使用变量](#11-使用变量)
	- [1.2. 只读变量](#12-只读变量)
	- [1.3. 删除变量](#13-删除变量)
- 2. 参数传递 

## 1. Shell变量

定义变量时，变量名不加 $ 符号，如：

	your_name="myname"

`注意：变量名和等号之间不能有空格，而且不能使用 bash 里边的关键字（使用 bash --help 查看bash命令的保留关键字）`

### 1.1. 使用变量
	
	your_name="tom"
	echo $your_name
	echo ${your_name}

变量名外面的花括号是可选的，加不加都行，加花括号是为了帮助解释器识别变量的边界，比如：

	echo "I am good at ${skill}Script"

像上边的括号的作用是为了告诉解释器 skill 是一个变量，如果不加 {} 解释器会把 skillScript 当作变量名来解析，推荐给所有变量加上花括号，这个类似于PHP的变量解释

已经定义过的变量可以被重新定义，如：

	your_name="tom"
	echo $your_name
	your_name="alibaba"
	echo $your_name

`注意：第二次赋值的时候不能写 $your_name="alibaba"，shell脚本只有在使用变量的时候才加 $ 符号，这是和PHP脚本的区别`

### 1.2. 只读变量

使用 readonly 命令可以将变量定义为只读变量，只读变量的值不能被改变。
	
	myname="self"
	readonly myname
	myname="selfs"

运行脚本，会报错：

	./test.sh: line 4: myname: readonly variable
	
`注意：也可以简单的直接在定义变量的时候指定可读性 readonly myname="self"`

### 1.3. 删除变量

使用 unset 命令可以删除变量。语法：

	unset variable_name