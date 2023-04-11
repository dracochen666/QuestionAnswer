Mysql 密码修改

首先关闭设置中的Mysql服务



① 进入mysql文件夹    cd /usr/local/mysql/bin    回车

②获取权限    sudo su    回车，然后输入开机密码，回车

③进入安全模式    ./mysqld_safe --skip-grant-tables &    回车

此时MySQL服务重新启动，我们重新打开一个终端（快捷键 command + N）

按照步骤进行操作
④输入    mysql     回车，进入mysql命令

按照步骤进行操作
⑤输入    use mysql;    回车，使用mysql这个库

⑥输入    flush privileges;    回车，此项是刷新MySQL系统权限，要不然不让你访问！

⑦输入    alter user 'root'@'localhost' identified by '123456';   回车，重置密码



⑧驶入    exit    退出mysql命令

接下来，回到刚才的终端

按照步骤进行操作
⑨输入    exit    退出命令

3.进行测试
①输入    mysql -uroot -p    回车

②输入刚才重置的新密码

③进去了，成功充值密码，妈妈再也不用担心我忘记密码了 作者：柚是一年春来到 https://www.bilibili.com/read/cv15582599 出处：bilibili