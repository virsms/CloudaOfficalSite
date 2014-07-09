# clouda-core API文档

## 全局属性

### ROOT_DIR

指向启动文件所在的目录， 一般这个目录是应用的根目录。

### USER_DIR

用户文件的目录， 默认为: ROOT_DIR/app/

### CONF_DIR

存放配置文件的目录，默认为: ROOT_DIR/conf/，系统将自动监测这个目录并载入下面的js文件

## 全局方法

### define(map)

clouda.define()的快捷方式

### watch(key1,key2,key3...keyN,callback);

clouda.watch()的快捷方式

### use(key);

同步返回key所表示的资源内容，如果不存在则返回undefined。

>温馨提示: 

>当文件中存在循环依赖时,可能导至一个或多个文件永远不能被载入,所以使用时应当小心这种情况


## 对象

### watcher

一个可监测自身改变的名称空间对象， 提供define、watch、remove、unwatch四个方法。

#### define(name,value)

用于定义或改变当前空间的下属资源。

	var base = new Watcher();
	base.define("a",1);
	
#### watch(name,handle,once)

用于监视一资源的变化,无论对应资源是否存在。

	var base = new Watcher();
		
		base.watch("a",function(){
			console.log("a is %s",a);
		});
		
		base.define("a",1);
		
console会打印出：

	a is 1

#### remove(name)

用于删除一个已定义的资源

	var base = new Watcher();
		
	base.remove("a");


#### unwatch(name,handle)

删除对一个资源的一个监视方法。

	var base = new Watcher();
		
	base.unwatch("a"，function(){
		console.log("Stop watch!");
	});

## clouda-core属性

### clouda.resource

一个watcher对像，用于存放在全局范围内运行时会改变的一类资源。

### clouda.config

一个watcher对像，用于存放在全局范围内运行时不会改变或很少改变的一类配置资源

### clouda.plugin

一个watcher对像，用于存放在在全局范内被公开访问的可执行的插件资源
	
## clouda-core方法

### clouda.define(obj);

提供一个简单的方式来定义多个资源。方法接收一个map，其key为使用"."分隔的资源名称的表示，前半部份应该为resource、config、plugin三者之一。

实例：

	clouda.define({
    	"config.clouda-httpserver":{
           	autoStart : true ,
        	defaultAction : function(){
            	this.send("Hello,World!");
        	}
    	}
	});
	
### clouda.watch(key1,key2,key3...keyN,callback);

提供一个简单的方式同时监测多个资源是否存在，当所有资源都存在时，执行一个回调并以相同的顺序将所需的资源传入callback中。其key应为使用"."分隔的资源名称的表示，前半部份应该为resource、config、plugin三者之一。

实例：

 	clouda.watch("plugin.talker",function(talker){
     	talker.say();
 	})

### clouda.requireDir(path,limit,isAbsPath);

用于载入一个目录下的js文件。

参数说明：

* path 

	目标路径，类型：string。

* limit
	
	用于限制载入文件的一个正则对像，只载入名称相配置的文件，而忽略其它的。类型：{RegExp}

* isAbsPath 
	
	用于标识path所表示的是否为绝对路径。当isAbsPath不为true且path以 "/" 开头时，将会自动在前面补充 `ROOT_DIR + path`进行访问，类型：Boolean，默认为`false`。
	
实例：

	//载入目录下以include开头的js文件
	clouda.requireDir("./",/include.*\.js/);


### clouda.createWatcher();

取得一个watcher对像的工厂方法