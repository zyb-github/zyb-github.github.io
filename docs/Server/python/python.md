### Python 安装pygame （Windows系统）

> ------
>
> 大致步骤：
>
> 1、安装pip（并设置国内镜像源）
>
> 2、安装wheel，可以用来安装whl文件
>
> 3、下载安装pygame
>
> ------

#### 1、安装pip

+ 浏览器输入网址 https://bootstrap.pypa.io/get-pip.py， 如果弹出文件下载框，则直接下载；如果网页出现大量文字，则复制所有文字，保存为get-pip.py文件。
+ 运行get-pip.py文件 ,cmd窗口定位到get-pip.py文件目录，输入命令行 python get-pip.py, 等待安装
+ 安装结束之后，输入 pip --version，出现版本信息则意味着安装成功
+ 一般安装后的pip都需要升级，cmd输入命令行 python -m pip install  --upgrade pip  ,等待升级

> pip配置国内数据源（两种方式）
>
> 1、临时数据源，每次安装都需要输入数据源，例如上面的更新pip，可以改为国内的数据源
>
>   ~~~python
> python -m pip install --upgrade pip -i https://pypi.tuna.tsinghua.edu.cn/simple/
> 
> 下面列举国内常用的pip源
> 阿里云 http://mirrors.aliyun.com/pypi/simple/
> 中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/
> 豆瓣(douban) http://pypi.douban.com/simple/
> 清华大学 https://pypi.tuna.tsinghua.edu.cn/simple/
> 中国科学技术大学 http://pypi.mirrors.ustc.edu.cn/simple/
>   ~~~
>
> 2、全局数据源，一次配置，永久使用
>
> + Window系统下：在系统中创建一个pip目录，如：C:\work-server\python\pip，在目录中新建文件 pip.ini。编辑文件写入一下内容（以设置清华源为例），文件编辑完成之后需要把C:\work-server\python\pip目录添加到环境变量之中，才可以生效
>
>   ~~~ini
>   [global]
>   index-url = https://pypi.tuna.tsinghua.edu.cn/simple/
>   [install]
>   trusted-host = https://pypi.tuna.tsinghua.edu.cn
>   ~~~
>
> + Linux系统下：修改 ~/.pip/pip.conf (没有就创建一个文件夹及文件。文件夹要加“.”，表示是隐藏文件夹)，编辑文件输入一下内容（以设置清华源为例）
>
>   ~~~ini
>   [global]
>   index-url = https://pypi.tuna.tsinghua.edu.cn/simple/
>   [install]
>   trusted-host = https://pypi.tuna.tsinghua.edu.cn
>   ~~~

#### 2、安装wheel

~~~Python
pip install wheel
~~~

#### 3、安装pygame

~~~txt
输入网址 https://www.lfd.uci.edu/~gohlke/pythonlibs/#pygame
其中cpxx代表python版本，win32代表32位系统，win_amd64代表64位系统，如python位3.5.1版本，64位windows系统,则下载
pygame‑1.9.6‑cp35‑cp35m‑win_amd64.whl文件
文件下载之后，cmd定位到文件地址输入命令行
python install pygame‑1.9.6‑cp35‑cp35m‑win_amd64.whl
等待安装完成
~~~

#### 4、测试是否安装成功

> cmd进入python模式
>
> 输入 import pygame 回车
>
> 不报错则证明安装成功。



