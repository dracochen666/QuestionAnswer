# ==访问权限==

----

### 访问权限

​	open > public > internal > fileprivate > private

​	open:允许所在模块和其他模块进行访问，并且允许继承和重写

​	public:允许所在模块和其他模块进行访问，但不允许其他模块进行继承和重写

​	internal(默认):只允许所在模块内进行访问，不允许其他模块访问

​	fileprivate:只允许在定义文件内进行访问

​	private:只允许在定义实体内进行访问

# ==属性==

----

### 存储属性和计算属性

​	存储属性：

​		

​	计算属性：

​		

# ==关键字==

----

### final

​	final可以用来修饰类、方法与属性，被修饰的都不可以继承

### class与static

​	都可以用来修饰静态属性或方法

#### 	区别：

​		1）static可以修饰类、结构体、枚举类型的计算属性、存储属性和方法；class只能修饰类的计算属性和方法	

​		2）static修饰的类方法无法继承；class修饰的类方法可以继承

# ==UI==

----

<img src="https://coffeechenbucket.oss-cn-beijing.aliyuncs.com/SwiftNotePictures/webp" alt="img" style="zoom:50%;" />

### **bounds和frame的区别**

frame: frame(x: 100, y: 100, width: 50, height: 50)
	当前视图相对于父视图的位置
bounds: bounds(x: -50, y: -50, width: 50, height: 50)
	bounds中的x，y值决定当前视图左上角的坐标是多少，也会影响子视图的位置。

<img src="https://coffeechenbucket.oss-cn-beijing.aliyuncs.com/SwiftNotePictures/picture%201.jpg" alt="picture 1" style="zoom:50%;" />

​	（csdn图片)



### clipToBounds和maskToBounds

clipToBounds(true,false)和maskToBounds都是用来限制子视图的显示范围不能超过父视图的边界(bounds)。clipToBounds是UIView的属性，maskToBounds是CALayer的属性，使用clipToBounds会调用maskToBounds。

**用法：** view.clipToBounds = true ; view.layer.maskToBounds







### **intrinsicContentsize是什么**

​	控件内部大小，UIView的一个属性。通过这个属性可以获取到控件与控件内容的间距，也重写继承UIView的控件的IntrinsicContentSize的值来改变间距。

<img src="https://coffeechenbucket.oss-cn-beijing.aliyuncs.com/SwiftNotePictures/image-20221025164803392.png" alt="image-20221025164803392" style="zoom: 25%;" />

**默认间距：**UILabel实例，文字居中

<img src="https://coffeechenbucket.oss-cn-beijing.aliyuncs.com/SwiftNotePictures/image-20221025165230541.png" alt="image-20221025165230541" style="zoom:50%;" />

**增加间距：**CustomLabel实例，文字居中

​	在CustomLabel对属性intrinsicContentSize进行重写，返回在父类默认值基础上修改的值

<img src="https://coffeechenbucket.oss-cn-beijing.aliyuncs.com/SwiftNotePictures/image-20221025165251341.png" alt="image-20221025165251341" style="zoom:50%;" />

``````swift
class CustomLabel: UILabel {
    override var intrinsicContentSize: CGSize {
        let contentSize = super.intrinsicContentSize
        return CGSize(width: contentSize.width + 16.0, height: contentSize.height + 8.0)
    }
}
``````

# ==多线程==

----

### **GCD是什么**

​	GCD(Grand Central Dispatch)

​	iOS的底层API，用于解决程序中的并发问题。

### Dispatch 调度

#### 	调度队列（DispatchQueue）

​		队列用于存储和管理待执行任务的容器。具体执行还是落实到CPU的线程上，所以需要指定同步或异步的执行策略。

​		**-按串行、并发分类-**

​		串行队列（SerialQueue）

​			当队列中存在多个任务时，任务是一个接一个按顺序分配执行的。		

​		并发队列（ConcurrentQueue）

​			当队列中存在多个任务时，多个任务是可以同时分配执行的



​		主队列（DispatchQueue.main/主线程，串行队列）

​			主队列是一个串行队列，并且运行在主线程是确定的。在主队列中添加同步任务后，新添加的任务会等待主队列完成后执行，主队列完成又需要完成添加的同步任务，由此造成死锁。		

#### 	同步与异步（sync,async）

​		本质区别是是否阻塞当前队列。实现异步的目的主要是防止任务阻塞，当一个任务的执行需要上一个任务的结果且上一个任务始终无法完成，就会造成阻塞，影响执行效率。

​		串行队列中

​			指定同步执行，串行队列中原本是一个接一个运行的，所以不会开启新线程。

​			指定异步执行，队列本身是一个线程，如果想实现异步，必须使用多线程，因此需要再开启一个线程用来执行指定任务。

​		并发队列中

​			指定同步执行，任务按顺序执行，不会开启新线程。

​			指定异步执行，队列本身是一个线程，如果想实现异步，必须使用多线程，因此需要再开启一个线程用来执行指定任务。

#### 主队列同步任务死锁

​	**原因：**主队列只能运行在主线程上， 在主队列中添加异步任务不会开启新线程，异步任务的闭包会在主队列任务结束后在主线程上执行，这是在主队列里实现异步的原理。如果在主队列中添加同步任务，**开启同步的代码**(main.sync)首先会阻塞主线程，然后**同步的任务**会添加至主队列等待执行，开启同步的代码结束需要执行闭包的代码，闭包又需要等待开启同步代码结束，形成死锁。

#### 自定义串行队列同步

​	主队列和自定义串行队列都是串行队列，但是在自定义串行队列中添加同步任务就不会死锁。

​	**原因：**自定义串行队列同步的情况下，相对于在主队列基础上添加了一个新的串行队。执行开启同步代码后，主队列会被阻塞等待，然后闭包内代码会被添加至自定义串行队列，由自定义串行队列分配至主线程上执行。执行完毕之后，再返回主队列，主队列阻塞解除，继续执行下一任务。

#### 自定义串行队列嵌套同步

​	在自定义串行队列同步或异步任务中再嵌套另一个同步任务会造成死锁的情况。

​	**原因：**在串行队列中添加同步任务A会阻塞当前队列，在主线程上执行闭包代码，而闭包代码中包含另一个同步任务B。假设没有新的同步任务B，那么任务A闭包中的代码可以顺序执行完后解除阻塞。但是同步任务A中包含了同步任务B，在任务A闭包代码顺序执行过程中，又给串行队列添加了新的任务，新任务需要等待同步任务A完成后才能执行，因而产生了死锁。

​	规律：死锁的发生条件 队列内产生阻塞，需要完成才能继续分配，而任务需要等待队列解除阻塞，并且没有其他队列用来分配任务。

<img src="https://coffeechenbucket.oss-cn-beijing.aliyuncs.com/SwiftNotePictures/image-20221108134552190.png" alt="image-20221108134552190" style="zoom:30%;" />

<img src="https://coffeechenbucket.oss-cn-beijing.aliyuncs.com/SwiftNotePictures/image-20221108134953517.png" alt="image-20221108134953517" style="zoom:33%;" />

<img src="https://coffeechenbucket.oss-cn-beijing.aliyuncs.com/SwiftNotePictures/image-20221108135041765.png" alt="image-20221108135041765"  alt="image-20221108134953517" style="zoom:33%;" />





### 线程安全

多个线程同时访问同一个资源时，可能会导致操作过程混乱，结果不为预期的情况。因此需要一些方法来避免多线程环境下对资源操作的产生错误的问题。

#### 锁（Lock）



#### 屏障（.barrier）

对任务添加.barrier屏障属性，在异步执行时，会阻塞线程执行完之前的任务后再执行当前任务。





# ==事件传递原理==

----

### 命中测试

​	命中测试的目的是找到第一响应者

	#### 3个条件

​	1、检查自身是否能接受事件（含任意一个都无法接受事件）

​		（1）view.isUserInteractionEnabled = false 用户是否可以交互

​		（2）view.alpha <= 0.01 透明度

​		（3）view.isHidden = true 是否隐藏

​	2、检查坐标是否在自身内部

​	3、检查自身有没有子视图

​		有子视图则继续递归检查子视图



### 响应链

​	响应链是由响应者组成的链表，响应链的表头是第一响应者。



# ==数据的编解码、传递、存储==

----

### **SwiftJSON作用：**

​	SwiftJSON是一个第三方包，简单来说，就是方便在swift中对json数据进行操作。在swift中进行网络请求后会收到json格式的数据，但是该格式在swift中不方便取值，SwiftJSON对json数据解析后，就可以通过链式方法取值



### **页面跳转方式：**

​	1.present/dissmiss

​	2.push/pop 



### **页面传值方法：**

​	可选型、隐式可选型：

​	系统更新视图，不会调用DataSource方法

​	reloadData()



### **本地存储方法：**

​	UserDefaults:

​		存数据：

​			调用UserDefaults.standard.set(value,key),value=data,在本地sandbox中以plist的形式存储

​		data是通过JSONEncoder()编码的数据,被编码的数据必须是基本数据类型或者遵循Codable(Encodable&Decodable)协议的数据类型

​		取数据：

​			调用UserDefaults.standard.data(key),取到的数据通过JSONDecoder().decode([Todo].self, from: data)解码，需要传入解码类型		（元类型：[String].self）和解码数据

​	CoreData:

​		CoreData是Swift中的一个数据仓库框架，能够方便开发者对数据库进行操作。



### 沙盒（Sandbox）

(引用CSDN)

沙盒是苹果的一种本地存储方式。

**Doucuments(保存程序生成的数据)**
	1.保存由应用程序产生的文件或者数据，例如：涂鸦程序生成的图片，游戏关卡记录
	2.iCloud会自动备份Doucument中的所有文件
	3.如果保存了网络下载的文件，在上架审批的时候，会被拒！
**Tmp(暂放数据,随时收回)**
	1.临时文件夹，保存临时文件
	2.保存在tmp文件夹的文件，系统会自动回收，譬如：磁盘空间紧张或者重新启动手机
	3.程序员不需要管tmp文件夹中的释放
**Caches(保存网络下载的数据)**
	1.缓存，保存从网络下载的文件，后续仍然需要继续使用，例如：网络下载的离线数据，图片，视频…
	2.缓存目录中的文件不会自动删除，可以做离线访问
	3.要求程序必须提供一个完善的清楚缓存目录的 解决方案
**Preferences(偏好设置)**
	1.系统偏好，用户偏好
	2.操作是通过[NSUserDefaults standardDefaults]来直接操作
<img src="https://coffeechenbucket.oss-cn-beijing.aliyuncs.com/SwiftNotePictures/image-20221114215303017.png" alt="image-20221114215303017" style="zoom:33%;" />



# ==内存机制==

----

### 内存异常

#### 		内存溢出

#### 		内存泄漏	

### 栈、堆

#### 			栈为什么比堆快:(引用自blog)

栈：是机器系统提供的数据结构，计算机会在底层对栈提供支持：分配专门的寄存器存放栈的地址，压栈出栈都有专门的指令执 行，这就决定了栈的效率比较高。

堆：为了分配一块内存，库函数会按照一定的算法在堆内存中搜索可用的足够大小的空间，如果没有足够大小的空间（可能是由于内存碎片太多），就有可能调用系统功能去增加程序数据段的 内存空间，这样就有机会分到足够大小的内存，然后进行返回。显然，堆的效率比栈要低得多。



### **深拷贝、浅拷贝**

#### 			深拷贝：

​			对实例进行拷贝时，拷贝指针和指针指向的值。拷贝后，相当于完全复制了原实例并且是独立的，不共享同一块内存空间，修改新实例不会影响原对象。

#### 		浅拷贝：

​			仅拷贝原实例的指针。拷贝后，指针指向同一块内存空间，修改新对象也会修改原实例。



### **swift 写时复制（Copy On Write）**

​	Swift中<u>值类型</u>在拷贝后会优先使用原实例的内存空间，当新实例的值发生改变后，才会重新开辟空间，由此可以节省内存空间。

​	图中创建arr1和arr2两个数组后，它们的地址是相同的。

<img src="https://coffeechenbucket.oss-cn-beijing.aliyuncs.com/SwiftNotePictures/image-20221027173831860.png" alt="image-20221027173831860" style="zoom:33%;" />

​	当改变arr2中内容后，arr2的内存地址发生了变化。

<img src="https://coffeechenbucket.oss-cn-beijing.aliyuncs.com/SwiftNotePictures/image-20221027173626517.png" alt="image-20221027173626517" style="zoom:33%;" />



### Swift 自动引用计数（Automatic Reference Counting）

#### 		ARC是什么

​			在Swift中，系统会对一个对象的被引用次数进行记录，如果引用它的数量为零（不再被使用），那么就会释放其所占用的内存（销毁）。

​			图中，仅当person的所有引用都为nil时才会调用deinit函数释放内存。

<img src="https://coffeechenbucket.oss-cn-beijing.aliyuncs.com/SwiftNotePictures/image-20221027210524614.png" alt="image-20221027210524614" style="zoom:33%;" />

#### 		ARC的作用

​		作为一个计数器帮助系统追踪和管理应用程序的内存空间。根据实例的引用计数值判断实例是否应该销毁。	

#### 		循环强引用

##### 概念

​				当两个或多个对象之间引用关系是非线性或者循环的（你中有我我中有你），会使它们的引用计数永不为0，无法自动销毁，从而导致内存泄漏。
​				图中Student中存在Teacher类的引用，Teacher类中存在Student类的引用。在它们实例化后，将他们赋值给对方的引用。可以发现，在尝试将student、teacher实例赋值nil后，控制台没有打印调用析构函数的信息，原因就是他们之间形成了循环引用。

​										<img src="https://coffeechenbucket.oss-cn-beijing.aliyuncs.com/SwiftNotePictures/image-20221027230423003.png" alt="image-20221027230423003" style="zoom:33%;" />

​										<img src="https://coffeechenbucket.oss-cn-beijing.aliyuncs.com/SwiftNotePictures/image-20221027230859332.png" alt="image-20221027230859332" style="zoom:28%;" />

​					当把产生循环引用的两个对象赋值为nil后并没有调用相应的析构方法，但是却无法打印两个对象的属性。这是因为两对象产生了循环引用导致引用计数永不为0，系统也就无法销毁对象释放内存，但是赋值操作使得两个对象都为空（对象存储的是值的地址），无法取到值存储的地址。该问题称为**内存泄漏**

​										<img src="https://coffeechenbucket.oss-cn-beijing.aliyuncs.com/SwiftNotePictures/image-20221030172945261.png" alt="image-20221030172945261" style="zoom:33%;" />

##### 发生循环引用的情况

​	1、多个类互相引用，形成引用循环

​	2、闭包内使用引用类型实例会使引用计数加1，如果在类的闭包内使用self就会造成循环引用，导致实例无法被销毁。



##### 避免循环引用

​				1.弱引用（weak）

​					（1）弱引用不会使引用计数加一

​					（2）弱引用对象必须为可选值

​					（3）在对象的引用计数为零后，弱引用的对象会自动设置为nil。

​									 <img src="https://coffeechenbucket.oss-cn-beijing.aliyuncs.com/SwiftNotePictures/image-20221101154540445.png" alt="image-20221101154540445" style="zoom:33%;" />

​				2.无主引用（unowned）

​					（1）无主引用不会使引用计数加一

​					（2）无主引用对象为非可选值类型

​					（3）无主引用对象必须确保对象始终不为nil，所以需要指向一个始终未被销毁的对象

####  lazy（懒加载）

​	被lazy修饰的变量会延时加载。

##### 作用

​	在程序加载过程中，有些属性不会被立即用到，可以让这些属性延时加载，以减少程序加载的时间。

# ==类与结构体==

----

### **结构体和类的区别**

​	1、结构体属于值类型，类属于引用类型	

​		结构体的拷贝方式属于深拷贝，类的拷贝方式属于浅拷贝。

（图中Person结构体实例的打印结果为person实例本身，而Person类实例的打印结果为地址，改地址指向存储该实例的堆内存空间。）

<img src="https://coffeechenbucket.oss-cn-beijing.aliyuncs.com/SwiftNotePictures/image-20221026202139992.png" alt="image-20221026202139992" style="zoom: 33%;" />

<img src="https://coffeechenbucket.oss-cn-beijing.aliyuncs.com/SwiftNotePictures/image-20221026202247011.png" alt="image-20221026202247011" style="zoom:33%;" />

​	2、类可以继承和实现协议，结构体不能继承，只可以实现协议。

​		结构体

# ==协议==

----

### **协议（Protocol）**

### 关键字

​	**optional** 

​		用optional修饰的方法可以选择性的实现。

# ==设计模式==

----

### 单例模式

一个类的初始化方法init()的权限是私有的，在类内有该类的实例化对象，可供外部调用。此模式可以使一个类只有一个实例化对象。

### 单一职责模式



# ==Mac终端的使用==

----

###  终端代理设置

​	**原因：**终端访问网络不走系统的代理，不设置代理的话下载源的访问会很慢甚至无法访问

​	**方法一：**临时写入终端

​		（1）终端执行：export http_proxy=http://proxyAddress:port

```bash
例：
export http_proxy=http://127.0.0.1:49234
```

​	**方法二：**永久写入终端

​		（1）终端执行：vim ~/.bashrc 访问Shell配置文件

​		（2）在.bashrc文件中添加：export http_proxy="http://localhost:port"  export https_proxy="http://localhost:port" ，保存文件

```bash
例：
export http_proxy=http://127.0.0.1:49234
export https_proxy=http://127.0.0.1:49234
```

​		（3）终端执行：source ~/.bashrc

### ruby、gem、brew

什么是rvm：ruby环境管理器，可以通过rvm对ruby环境进行管理和切换。

什么是ruby：ruby是一种编程语言。与Java类似需要jdk来搭建开发环境，不过现在的MacOS中自带ruby开发环境。

什么是gem：gem是ruby库管理器，cocoapods就是ruby构建的库之一，所以cocoapods可以通过gem进行安装。

什么是brew：Homebrew也是软件包管理工具，范围更广，gem只管理ruby相关软件。

### Homebrew

（1）Homebrew安装。

```bash
##终端执行
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"
```

（2）Homebrew卸载。

```bash
##终端执行
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/HomebrewUninstall.sh)"
```



export PATH="/usr/local/Cellar/cocoapods/1.11.3"

### cocoapods

#### 	cocoapods安装：

​			第三方依赖库管理工具，通过pod在终端安装第三方库。

​			**ruby -> gem install** Or **brew install**

（1）卸载本机的cocoapod

```bash
sudo rm -rf /usr/local/bin/pod
```

（2）根据gem list找到cocoapods相关的库并卸载

```bash
gem list
*** LOCAL GEMS ***
cocoapods (1.11.2)
cocoapods-core (1.11.2)
cocoapods-deintegrate (1.0.5)
...
```

```bash
sudo gem uninstall cocoapods (1.11.3)
sudo gem uninstall cocoapods-core (1.11.3)
sudo gem uninstall cocoapods-deintegrate (1.0.5)
sudo gem uninstall cocoapods-downloader (1.6.3)
sudo gem uninstall cocoapods-plugins (1.0.0)
sudo gem uninstall cocoapods-search (1.0.1)
sudo gem uninstall cocoapods-trunk (1.6.0)
sudo gem uninstall cocoapods-try (1.2.0)
```

（3）安装cocoapods

```bash
sudo gem install cocoapods
```



#### 	cocoapods使用：

##### 			安装库：

（1）将地址设为项目根目录 

```bash
##终端执行
cd /项目目录
```

（2）对项目进行初始化

```bash
##终端执行
pod init
```

（3）修改项目Podfile文件

```bash
##例子 Podfile文本 添加 pod 'SnapKit', '~> 5.6.0'
platform :ios, '10.0'
use_frameworks!

target 'PullToRefreshTest' do
    pod 'SnapKit', '~> 5.6.0'
end
```

（4）安装依赖

```bash
pod install
```



### 其他问题

#### Shell命令失效：

（1）终端执行此命令可暂时使用Shell命令

```bash
##终端执行
export PATH="/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin:/usr/X11/bin"
```

（2）进入.zshrc文件，将（1）步命令存入.zshrc最后一行，保存

```bash
open ./.zshrc
```

（3）重启终端

#### Frameworks not found XXX 框架未找到：

（1）查看项目启动文件是否为XXX.xcworkspace，使用XXX.xcodeproj启动项目会出现问题

# ==Git==

----

###  远程连接

**步骤：**（本地已有仓库连接远程仓库）

1、初始化本地仓库

```bash
##终端输入
cd 项目目录
git init
```

2、创建远程空仓库

在github中创建一个新的空仓库。

3、连接远程仓库

```bash
##终端输入 （仓库地址 例：https://github.com/dracochen666/Algorithms.git）
git remote add origin 仓库地址
```

4、添加全部本地文件

```bash
##终端输入 
git add .
```

5、提交本地文件

```bash
##终端输入 
git commit -m '描述'
```

6、推送本地到远程仓库

```bash
##终端输入 (例：git push -u origin main)
git push -u 本地分支 远程分支
```

# ==XX==

----

###  11/14

1、final修饰的类能不能继承 √

2、访问权限 √

3、懒加载 √

4、响应链

5、栈为什么堆快 √

6、class和static区别 √



7、协议可选方法 √

8、cliptoBounds √

9、发生循环应用的情况，闭包的循环引用 √

10、sandbox文件内容 √

11、看文章 https://juejin.cn/post/6860694444413288455#comment





### 11/30

1、contentmode 与 distribute

2、抗压缩优先级与抗拉伸优先级

3、KVO与KVC



### 12/3

1、addSubview 与 insertSubview

2、inset 与 offset (https://www.jianshu.com/p/0f91d55da8c6) (https://www.jianshu.com/p/f0eeef56f391)

3、逃逸闭包和非逃逸闭包

4、单一职责设计模式

5、UIView的生命周期（https://blog.csdn.net/yong_19930826/article/details/112258284）

6、window

7、UIScrollView



### 12/6

1、cocoapods 安装 √

2、使用pod安装github库 √





