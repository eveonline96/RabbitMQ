##RabbitMQ的远程Web管理与监控工具

RabbitMQ提供了完善的管理和监控工具，分management plugin 和 rabbitmqctl 两种类型的工具。

###1、management plugin 
rabbitmq-management plugin提供HTTP API来管理和监控RabbitMQ Server，具体包含如下功能：

* 删除、生成、列表，包括：exchanges，queues，bindings，users，virtual hosts and permissions。

* 监视 queue 长度，每个 channel的message rates ，每个连接的data rates，等等。

* 发送和接收messages。

* 监控Erlang processes，file descriptors，memory use。

* 导出／导出object definitions to JSON。

* 强制关闭 connections，清空 queues。

* management plugin默认就在RabbitMQ的发布版本中，只需要enable就可以了，执行以下命令：

	$ cd /usr/lib/rabbitmq/bin/
	$ sudo rabbitmq-plugins enable rabbitmq_management

![](https://github.com/silence940109/RabbitMQ/blob/master/doc/web_management/image/webmanagement.jpg)

看到这一幕后，重启RabbitMQ就可以了。需要注意的是，mochiweb 必须得有，mochiweb是一个用来构建Web应用的轻便，高效的HTTP应用框架的Erlang库。

下面可以通过以下两种方式来访问管理和监控界面：

###2、Web UI管理消息服务器

![](https://github.com/silence940109/RabbitMQ/blob/master/doc/web_management/image/webmanagement.png)


接下来你就可以掌控RabbitMQ了。

###3、添加远程管理账户

如果要从远程登录怎么做呢？处于安全考虑，guest这个默认的用户只能通过http://localhost:15672来登录，其他的IP无法直接用这个guest帐号。这里我们可以通过配置文件来实现从远程登录管理界面，只要编辑/etc/rabbitmq/rabbitmq.config文件（没有就新增），添加以下配置就可以了。

	[{rabbit, [{tcp_listeners, [5672]}, {loopback_users, ["asdf"]}]}].

现在添加了一个新授权用户asdf,可以远程使用这个用户名。记得要先用命令添加这个命令才行：

	$  cd /usr/lib/rabbitmq/bin/
	#用户名与密码
	$ sudo rabbitmqctl add_user asdf 123456
	#用户设置为administrator才能远程访问
	$ sudo rabbitmqctl set_user_tags asdf administrator         
	$ sudo rabbitmqctl set_permissions -p / asdf ".*" ".*" ".*

其实也可以通过管理平台页面直接添加用户和密码等信息。如果还不能远程访问或远程登录检查是不是5672, 15672端口没有开放！！！！！！

###4、远程管理接口的命令行工具
Management command line tool（rabbitmqadmin）拥有跟Web UI同样的功能，可能还更便于脚本使用。rabbitmqadmin仅仅是一个定制的HTTP客户端，如果想在自己的程序中引入 rabbitmqadmin的话，可以直接使用HTTP API。rabbitmqadmin可以在http://server-name:15672/cli页面下载。

下载rabbitmqadmin.txt后，将其重命名为rabbitmqadmin，拷贝到 /usr/local/bin 或者你想要的路径，并增加可执行权限。使用：
	
	sudo rabbitmqadmin --help
	
可以查看使用指南。

###5、rabbitmqctl命令行管理工具
rabbitmqctl是一个简单的命令行工具用于管理RabbitMQ Server，适合通过ssh登陆的管理。

在shell中执行如下命令，能看到各种命令简价：

	stop [<pid_file>]  
	stop_app  
	start_app  
	wait <pid_file>  
	reset  
	force_reset  
	rotate_logs <suffix>  
	hipe_compile <directory>  
	join_cluster <clusternode> [--ram]  
	cluster_status  
	change_cluster_node_type disc | ram  
	forget_cluster_node [--offline]  
	rename_cluster_node oldnode1 newnode1 [oldnode2] [newnode2 ...]  
	update_cluster_nodes clusternode  
	force_boot  
	sync_queue [-p <vhost>] queue  
	cancel_sync_queue [-p <vhost>] queue  
	purge_queue [-p <vhost>] queue  
	set_cluster_name name  
	add_user <username> <password>  
	delete_user <username>  
	change_password <username> <newpassword>  
	clear_password <username>  
	authenticate_user <username> <password>  
	set_user_tags <username> <tag> ...  
	list_users  
	add_vhost <vhost>  
	delete_vhost <vhost>  
	list_vhosts [<vhostinfoitem> ...]  
	set_permissions [-p <vhost>] <user> <conf> <write> <read>  
	clear_permissions [-p <vhost>] <username>  
	list_permissions [-p <vhost>]  
	list_user_permissions <username>  
	set_parameter [-p <vhost>] <component_name> <name> <value>  
	clear_parameter [-p <vhost>] <component_name> <key>  
	list_parameters [-p <vhost>]  
	set_policy [-p <vhost>] [--priority <priority>] [--apply-to <apply-to>] <name> <pattern>  <definition>  
	clear_policy [-p <vhost>] <name>  
	list_policies [-p <vhost>]  
	list_queues [-p <vhost>] [--offline|--online|--local] [<queueinfoitem> ...]  
	list_exchanges [-p <vhost>] [<exchangeinfoitem> ...]  
	list_bindings [-p <vhost>] [<bindinginfoitem> ...]  
	list_connections [<connectioninfoitem> ...]  
	list_channels [<channelinfoitem> ...]  
	list_consumers [-p <vhost>]  
	status  
	node_health_check  
	environment  
	report  
	eval <expr>  
	close_connection <connectionpid> <explanation>  
	trace_on [-p <vhost>]  
	trace_off [-p <vhost>]  
	set_vm_memory_high_watermark <fraction>  
	set_vm_memory_high_watermark absolute <memory_limit>  
	set_disk_free_limit <disk_limit>  
	set_disk_free_limit mem_relative <fraction>  
	encode [--decode] [<value>] [<passphrase>] [--list-ciphers] [--list-hashes]   
	<span style="white-space:pre">  </span>   [--cipher <cipher>] [--hash <hash>] 
		[--iterations <iterations>]  
