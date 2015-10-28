#Swift中?和!的使用

初学Swift语言者，对?和!很迷茫，不知道什么时候该用?，什么时候要用!，我第一次写了一个ViewController，在设置tableView的属性时，发现下面这段代码，都可以让程序正确运行，所以我比较困惑。

	class MyViewController:UIViewController,UITableViewDelegate,UITableViewDataSource
	{
		var tableView:UITableView? = UITableView()
	
		override func viewDidLoad()
		{
			super.viewDidLoad()
	
			tableView?.delegate = self
			tableView!.dataSource = self
			view.addSubview(tableView)
		}
	}

这里仅仅是截取了代码片段，我们看到在给tableView设置代理的时候，使用?和!能达到同样的效果，于是我查了一些文档，终于找到了答案，也知道?和!的确切用法。

##!的使用

* 对可选型(Optional)进行强制拆包，当然，前提是确定待拆包的可选型是有值的

		var optionalString:String?
	
		if optionalString != nil {
			print("\(optionalString!)") 
		}else{
			print("optionalString is nil")
		}
	
* 隐式解析可选类型

		var optionalString:String!
		if optionalString != nil {
			print("\(optionalString)") 
		}else{
			print("optionalString is nil")
		}
  
  可以看到打印语句的不同之处。
  
##?的使用

* 声明Optional值变量

		var label:UILabel?

* 用在对Optional值的操作中，用来判断是否能响应后面的操作

		tableView?.delegate = self

* 用于安全调用delegate的optional方法

		@objc protocol Downloadable {
	    	@optional func download(toPath: String) -> Bool;
		}

		@objc class Content: Downloadable {
	    //download method not be implemented
		}

		var delegate: Downloadable = Downloadable()
		delegate.download?("some path")


* 使用as?向下转型(DownCast)

		if let dataSource = object as? UITableViewDataSource {
		    let rowsInFirstSection  = dataSource.tableView(tableView, numberOfRowsInSection: 0)
		}
