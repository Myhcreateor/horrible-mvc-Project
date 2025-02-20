<p align="center">
    <img width="400px" src="https://github.com/Drinkwang/drinkwang.github.io/blob/master/img/git.png?raw=true">    
</p>

# 这是我的一个mmo 框架的压缩版


# 框架的内容

```内容构成
* controller
* model
* view
* appfactory
* ovserver

```

我们一个个来分析用途，controller这个类来看，也就是controller.cs是一个类命令模式、但是这里不进行处理命令，而是责任链的结构，在执行命令的时候看是否匹配消息链上的数据进行执行，不过不用太关注这些设计模式的命名，我也是用的时候去查了下，接下来用初学者能够理解的方式进行源码分析

```c#
using System.Collections;
using System.Collections.Generic;

using UnityEngine;
public class controller  {
	private Dictionary<string,IC> CommandFlow;
	// Use this for initialization
	public controller()
	{
		CommandFlow = new Dictionary<string, IC> ();
	}
	public void AdjustCommand(string msg,IC i)
	{
		if (!CommandFlow.ContainsKey (msg)) {
			CommandFlow.Add (msg, i);

		}

	}
	public void Todo(Observer o)
	{
		if (CommandFlow.ContainsKey (o.msg)) {
			CommandFlow [o.msg].Todo (o);
			Debug.Log (CommandFlow[o.msg].ToString ());

		}

	}

}
```



首先用Dictionry[字典]的形式定义一个命令流` CommandFlow`,然后我们需要二个方法，一个是在命令流里添加新的命令，也就是`adjustCommand` 这个方法实现很简单，就是简单判断是否曾包含命令，如果没有，则将其添加到命令流` CommandFlow`当中，其实这个方法也可以不用字典进行实现，比如说用List<T>或者数组也是可以的，但是字典是用Hash实现的，Hash碰撞的时间复杂度是o(1),当然用字典是最好的啦

剩下一个方法则是执行命令，从命令流中找到一个匹配的数据，我们包装一个`Observer`对象如下

```c#

using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Observer  {
	
	public string msg;
	public object body;

	// Use this for initialization

	public Observer(string msg,object body)
	{this.msg = msg;
		this.body = body;
	
	}
	public Observer(string msg):this(msg,null)
	{

	}


}

```

这里简易框架`Observer`对象就只有二个参数，一个`msg`，一个`body`，也可以选择性添加新参数，这样框架的使用更为灵活，这个是搭配controller的命令流使用的，可以将其作为参数传入Ic的 调用Todo中进行使用，我们再来看看Ic对象
```C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public  interface  IC  {


  void Todo (Observer o) ;
}
```
IC对象是一个接口，就只有todo一个方法，为了便于理解，我们用一个Ic实例进行详细介绍
```c#

using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class GoodsCommand : IC
{
    PackProxy packproxy = PackProxy.instances();
    Goodproxy goodproxy = Goodproxy.instances();


    public void Todo(Observer io)
    {
		if(io.msg=cmd.AddGoodCommand){
	        int id = 1;
   	    	Packagemodel model = null;
        	if (!int.TryParse(io.body.ToString(), out id))
        	{
            	return;
        	}
        	if (packproxy.TryGeGoodtModel(id, out model))
        	{
            	++model.count;
            	packproxy.update(id, model);
        	}
        	else if (packproxy.isfull())
        	{
            	return;
        	}
        	else
        	{
            	model = packproxy.getback();
            	model.goodid = id;
            	model.count = 1;

        	}
        	AppFactory.instances.ViewTodo(new Observer("RendertoViewcommand", "main"));

		}else if(io.msg=UseGoodCommand/*xxx是其他命令名*/){

			//xxxxxx具体实现省略
		}
    }
}
```
可以看出这里实际是操控Model的内容，然后通过Appfactory调用ViewTodo(为了方便理解，这里写的是ViewTodo，实际上可以包装一层，用专门的todo来调用ViewTodo等等)

以上则是这个框架的MV相关的介绍，至于View的内容，实际上是对这个内容粗略的模仿，大家可以自行研究，有任何不懂的或者bug，可以发一个issue给我，多谢

## Authors

* **Drinker·wang** - [Github](https://github.com/Drinkwang)
<br>![B站 Follow](https://space.bilibili.com/13061595)  

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details