# Uhouzz objc 编码规范（下）
======================

<a name='TOC'/></a>目录
----
* [代码格式化](#formatting)
  * [基本规范](#formatting-basic)
  * [空格](#spaces)
  * [花括号](#braces)
  * [折行](#line-wrap)
  * [对齐方式](#alignment-type)
  * [代码分组](#format-codegroup)
  * [黄金路径](#best-path)
  * [BOOL的使用](#bool-use)
  * [Switch-Case](switch-case)
* [注释格式规范](#comment)
  * [块注释](#block-comment) 
* [需要注意的问题](#question)
* [参考](#reference)


<a name='formatting'/></a>代码格式化
-----

### <a name='formatting-basic'></a>基本规范
* 函数长度（行数）不应超过2/3屏幕，禁止超过70行。
例外：对于顺序执行的初始化函数，如果其中的过程没有提取为独立方法的必要，则不必限制长度
* 单个文件方法数不应超过30个
* 不要按类别排序（如把IBAction放在一块），应按任务把相关的组合在一起
* 禁止出现超过两层循环的代码，用函数或block替代。

### <a name='spaces'></a>空格
类方法声明在方法类型与返回类型之间要有空格。

```objc
// 糟糕
-(void)methodName:(NSString *)string;

// OK
- (void) methodName:(NSString *)string;
 ↑空格 
```

条件判断的括号内侧不应有空格。

```objc
// 糟糕
if ( a < b ) {
    // something
}

// OK
if (a < b) {
  ↑  ↑<↑  ↑
    // something
}
```

关系运算符（如 `>=`、`!=`）和逻辑运算符（如 `&&`、`||`）两边要有空格。

```objc
// OK
(someValue > 100)?YES:NO

// OK
(someValue > 100) ? YES : NO
```

属性

```objc
@property (nonatomic, copy) NSString *name;  
空格↑        空格↑    ↑空格
```

二元算数运算符两侧是否加空格不确定，根据情况自己定。一元运算符与操作数之前没有空格。

多个参数逗号后留一个空格（这也符合正常的西文语法）。


### <a name='braces'></a>花括号
方法的花括号推荐另起一行。方法内部需要写在一行。

```objc
  - (void)methodName:(NSString *)string {
   ↑空格                                ↑空格，推荐花括号在一行
      if () {
     空格↑  ↑空格，花括号不要另起一行
      } else {
  要换行↑    ↑空格，花括号不要另起一行
      }    
  }
```

**动机**
> Xcode 默认的花括号位置是这样的：方法内部的各种补全都是在同一行的；方法定义的比较混乱，默认模版另起一行，但从 Interface Builder 中连线生成的方法在同一行的。
> 
> 考虑到 Xcode 的默认行为，方法内部要另起一行，方法所在行不强制定死。另外，模版可以定制，而 IB 生成的代码不可定制，所以不另起一行的写法优先。
>
> 另起一行的写法在代码折叠后非常难看。
> 

### <a name='line-wrap'></a>折行
统一使用自动折行。
如果修改自动折行宽度设置？
Xcode > Preferences > Text Editing > Page guide at column:中将最大行长设置为120

**动机**
> 手动折行的效果严重宽度依赖于窗口宽度——窗口过宽浪费宝贵的屏幕空间，较窄时可能无法阅读。而且 Xcode 自动折行的效果还是不错的。

### <a name='alignment-type'></a>对齐方式

当一个函数比较长超过120个字符时 我们使用参数对齐方式

```objc
// bad 
[self.navigationController postMessageToParentViewController:@{ @"hasNewComment":@(self.hasNewComment) }
                                                       viewController:self];
   

//good 这里尽量手动调整一下，自动折行不会自动对齐
- (void)startWithCompletionBlockWithSuccess:(void (^)(YTKRequest *request))success
                                    failure:(void (^)(YTKRequest *request))failure;
                                                    
```                                                   

<a name='format-codegroup'></a>代码分组
----

实现文件中的代码结构，提倡以下约定：

* 用`#pragma mark -`将函数或方法按功能进行分组。
* dealloc方法放到实现文件的最顶部。
> 这样是为了时刻提醒你要记得释放相关资源。

* delgate或协议相关方法放到一般内容之后。

```objc
#pragma mark - Lifecycle

- (void)dealloc {}
- (instancetype)init {}
- (void)viewDidLoad {}
- (void)viewWillAppear:(BOOL)animated {}
- (void)didReceiveMemoryWarning {}

#pragma mark - Custom Accessors

- (void)setCustomProperty:(id)value {}
- (id)customProperty {}

#pragma mark - Protocol conformance
#pragma mark - UITextFieldDelegate
#pragma mark - UITableViewDataSource
#pragma mark - UITableViewDelegate

#pragma mark - NSCopying

- (id)copyWithZone:(NSZone *)zone {}

#pragma mark - NSObject

- (NSString *)description {}
```


<a name='best-path'></a>黄金路径
----

```objc
//注意{} 推荐使用
if (!error) {
    return success;
}

//禁止使用
if (!error)
    return success;

// 为了简化示例，没有错误处理，并使用了伪代码

// 糟糕的例子
- (Task *)creatTaskWithPath:(NSString *)path {
    Task *aTask;
    if ([path isURL]) {
        if ([fileManager isWritableFileAtPath:path]) {
            if (![taskManager hasTaskWithPath:path]) {
                aTask = [[Task alloc] initWithPath:path];
            }
            else {
                return nil;
            }
        }
        else {
            return nil;
        }
    }
    else {
        return nil;
    }
    return aTask;
}

// 改写的例子
- (Task *)creatTaskWithPath:(NSString *)path {
    if (![path isURL]) {
        return nil;
    }
    
    if (![fileManager isWritableFileAtPath:path]) {
        return nil;
    }
    
    if ([taskManager hasTaskWithPath:path]) {
        return nil;
    }
    
    Task *aTask = [[Task alloc] initWithPath:path];
    return aTask;
}

还有一个例子

//错误写法
if (responsCode == 0) {
    if (count > 0) {
        NSLog(@"responseSuccess");
        //处理数据
        //其他操作
        //....
        //刷新UI
    }
} else {
    NSLog(@"Error");
}

//正确写法 先处理异常情况
if (responsCode ！= 0) {
    NSLog(@"Error");
    return;
} 

//处理正常情况
if (count > 0) {
    NSLog(@"responseSuccess");
    //处理数据
    //其他操作
    //....
    //刷新UI
}

```


<a name='bool-use'/></a>BOOL的使用
----
Ojbective-C 中把 BOOL 定义成无符号字符型，这意味着 BOOL 类型的值远不止`YES``(1)`或`NO``(0)`，不要直接把整形转换成`BOOL`

推荐：

```objc
if (someObject)
{
  // code...
}

if (![anotherObject boolValue]) 
{
  // code...
}
```

不推荐：

```objc
if (someObject == nil)
if ([anotherObject boolValue] == NO)
if (isAwesome == YES)     // 永远不要直接比较BOOL变量和YES/NO.
if (isAwesome == true)    //  永远不要直接比较BOOL变量和 True/False.

```

<a name='switch-case'/></a>Switch-Case
----
Case 语句  
* 当一个 case 语句包含多行代码时，必须要加上大括号。

```objc
switch (condition) 
{
  case 1:
    // ...
    break;

  case 2: {
    // ...
    // 多行 case 语句需使用大括号
    break;
  }

  case 3:
    // ...
    break;

   default: 
    // ...
    break;
}
```

* 当在 Switch 中使用枚举类型时，`default`块是不需要的。例如：

```objc
RWTLeftMenuTopItemType menuType = RWTLeftMenuTopItemMain;
 
switch (menuType) {
  case RWTLeftMenuTopItemMain: 
	// ...
    break;
  case RWTLeftMenuTopItemShows:
	// ...
    break;
  case RWTLeftMenuTopItemSchedule: 
	// ...
    break;
}
```

* `Case fall-through`问题
在某些情况需要同一份代码作用在多个 case 语句中，这种情况可以使用 fall-through，即在相应的 case 块中去掉`break;`语句。例如：  
```objc
switch (condition) 
{
  case 1:
    // ** fall-through!, 此处无 break; **
  case 2:
    // code executed for values 1 and 2
    break;

  default: 
    // ...
    break;
}
```

<a name='comment'></a>注释格式规范
----
尽量让代码可以自表述，而不是依赖注释。

> 注释应该表达那些代码没有表达以及无法表达的东西。如果一段注释被用于解释一些本应该由这段代码自己表达的东西，我们就应该将这段注释看成一个改变代码结构或编码惯例直至代码可以自我表达的信号。我们重命名那些糟糕的方法和类名，而不是去修补。我们选择将长函数中的一些代码段抽取出来形成一些小函数，这些小函数的名字可以表述原代码段的意图，而不是对这些代码段进行注释。尽可能的通过代码进行表达。你通过代码所能表达的和你想要表达的所有事情之间的差额将为注释提供了一个合理的候选使用场合。对那些代码无法表达的东西进行注释，而不要仅简单地注释那些代码没有表达的东西。”[^2]

### <a name='block-comment'></a>块注释
方法内部禁止使用块注释。除非要临时注释大段代码，一般情况总应使用行注释。

**动机**
> 因为块注释不能正确嵌套。


<a name='question'/></a>需要注意的问题
----

### 使用成员变量还是使用属性？
禁止在类的 interface 中定义任何 iVar 成员，只允许使用属性，但可以在特定情形中使用属性生成的 iVar。

尽量总是使用点操作符访问属性，而不是属性生成的 iVar 变量。以下情形除外：

* 明确要避免修改产生 KVO 通知的；
* 需重写属性 getter 或 setter 的；
* 性能分析确定使用属性会导致性能不可接受的；
* 多线程环境中，为防止互斥一次进行多个修改的；
* `init`、`dealloc`、`initWithCoder` 等方法中。

推荐

```objc
@interface MyAppTutorial : NSObject

@property (nonatomic, copy) NSString *tutorialName;

@end
```
不推荐

```objc
@interface MyAppTutorial : NSObject {
  NSString *tutorialName;
}
```


动机
> 如果使用 iVar，很多情况要特殊处理，容易出错。总是使用成员，规则简单，不易出问题。
> 
> 直接访问 iVar 的 block 会 retain iVar 所属的对象，这点很容易被忽略
>
> 定义和使用 iVar 都会产生编译警告，只不过默认设置没启用这两个警告

### 什么时候使用 copy？

* block 属性要定义成 copy。
* 当一个属性赋值后不期望改变时应当用 copy，最常见的类型如 NSString、NSURL。可变类型的成员，如 NSMutableArray、NSMutableDictionary 不能定成 copy 的。

相关 Demo 可在 https://github.com/BB9z/PropertyTest 获得。


<a name='reference'></a>参考
------
* [Coding Guidelines for Cocoa](http://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* https://github.com/raywenderlich/swift-style-guide
* https://github.com/github/swift-style-guide
* https://github.com/github/objc-conventions
* https://github.com/jverkoey/iOS-Best-Practices
* https://github.com/troyharris/Complete-iOS-StyleGuide
* https://github.com/objc-zen/objc-zen-book
* [你们是如何为 View Controller 的变量命名的呢？ - V2EX](//www.v2ex.com/t/25732)
* [代码大全(第2版) - 亚马逊](http://www.amazon.cn/dp/B0061XKRXA)
* [Wonderful objc Style Guide](https://github.com/markeissler/wonderful-objc-style-guide)

<a name='footnote'></a>
[^1]: [再谈ARC - 苹果核](http://pingguohe.net/2012/06/22/talk_arc_again/)
[^2]: [只对代码无法表达的东西写注释 - Tony Bai](http://bigwhite.blogbus.com/logs/125602412.html)
