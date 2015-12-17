# Uhouzz Objective-C 编码规范（上）
======================

<a name='TOC'/></a>目录
----
* [命名格式规范](#naming)
  * [基本原则](#naming-basic-principle)
  * [命名空间](#namespace)
  * [大小写](#naming-match-case)
  * [缩写](#abbreviation)
  * [其他](#naming-others)
  * [源文件命名](#naming-file)
  * [资源命名](#naming-resource)
  * [函数命名](#naming-method)
        * [参数命名](#naming-method-parmas)
        * [存取函数](#naming-method-accessor) 
        * [委托函数](#naming-method-delegate)
        * [集合操作函数](#naming-method-collection)
  * [协议命名](#naming-protocol)
  * [属性命名](#naming-properties)
  * [通知命名](#naming-notifications)
  * [临时变量命名](#naming-temporary-variable)
  * [常量命名](#naming-constant)

<a name='naming'/></a>命名格式规范
-----
### <a name='naming-basic-principle'></a>基本原则

* 清晰：  

命名应该尽可能的清晰和简洁，但在Objective-C中，清晰比简洁更重要。由于Xcode强大的自动补全功能，我们不必担心名称过长的问题。   
 
```Objective-C
//清晰
insertObject:atIndex:

//不清晰，insert的对象类型和at的位置属性没有说明
insert:at:

//清晰
removeObjectAtIndex:

//不清晰，remove的对象类型没有说明，参数的作用没有说明
remove:

不要使用单词的简写，拼写出完整的单词
    //清晰
destinationSelection
setBackgroundColor:

//不清晰，不要使用简写
destSel
setBkgdColor:
```

* 一致性  

整个工程的命名风格要保持一致性，最好和苹果SDK的代码保持统一。不同类中完成相似功能的方法应该叫一样的名字，比如我们总是用count来返回集合的个数，不能在A类中使用count而在B类中使用getNumber

* 结构统一
    
变量命名推荐的命名语素顺序是：最开头是命名空间简写，然后越重要、区别度越大的语素越要往前放。经典的结构是：作用范围+限定修饰+类型。例：

```C
extern ushort APIDefaultPageSize;        // 还行，能明白意思了
extern ushort APIDefaultFetchPageSize;   // 加上些限定更好一些
extern ushort APIFetchPageSizeDefault;   // 再好些，把重要的往前放

YHToolbarComment    // 不推荐
YHCommentToolbar    // OK，把类型（toolbar）置后
```

### <a name='namespace'></a>命名空间
* 前缀由大写的字母缩写组成，比如Cocoa中NS前缀代表Founation框架中的类，IB则代表Interface Builder框架。
* 类名、protocols、C 函数、常量、结构体和枚举应带有命名空间前缀；
* 注意不要为成员变量或者方法使用前缀，因为他们本身就包含在类的命名空间内

### <a name='abbreviation'/></a>缩写
可以使用广泛使用的缩写，如 `URL`、`JSON`，并且缩写要大写。但像将`download`简写为`dl`这种是不可以的。


```Objective-C
// OK
ID, URL, JSON, WWW

// 糟糕
id, Url, json, www

然而，有部分单词简写在Objective-C编码过程中是非常常用的，以至于成为了一种规范，这些简写可以在代码中直接使用，下面列举了部分：

alloc   == Allocate					max    == Maximum
alt     == Alternate				min    == Minimum
app     == Application				msg    == Message
calc    == Calculate				nib    == Interface Builder archive
dealloc == Deallocate				pboard == Pasteboard
func    == Function					rect   == Rectangle
horiz   == Horizontal				Rep    == Representation (used in class name such as NSBitmapImageRep).
info    == Information				temp   == Temporary
init    == Initialize				vert   == Vertical
int     == Integer

不要使用单词的简写，拼写出完整的单词
destinationSelection       // OK
destSel                    // 糟糕
setBackgroundColor:        // OK
setBkgdColor:              // 糟糕
```
### <a name='naming-match-case'></a>大小写
* 类名采用大驼峰（`UpperCamelCase`）
* 类成员、方法、参数小驼峰（`lowerCamelCase`）
* 局部变量大小写小驼峰，
* C函数的命名用大驼峰



### <a name='naming-file'></a>源文件命名
* 视图 ***View
* Cell ***Cell
* Controller
为了举例，我们假定有 `User`、`Tag`、`Category` 这几种 model 类型。

对象展示一般分列表和单个详情，其 view controller 分别使用 **Model**ListController 和 **Model**DetailController，推荐的语素顺序是：`Model名 + 限定与修饰 + ListController|DetailController`。举例说明：

```
// OK
TagUserUsedListController
TagInCategoryListController
CategoryDetailController

// 不推荐，列表统一使用 ListController，不指明是 table view 还是 collection view
UserFollowerTableViewController

// OK
UserFollowerListController

// 不推荐
UserLikedTagListController

// OK，把显示的对象放在第一位
TagUserLikedListController

// 糟糕，如果是 view controller，必须以 Controller 或 Displayer 结尾
TagListView
```

经常为了便于多个界面复用，我们会把 model 的显示统一在一个 view controller 中，在其他界面嵌入这个 view controller。我们把这类专门管理显示的 view controller 叫做 `displayer`。如：

```
UserListDisplayer
TagListDisplayer
```

UIView 级别的组件不要以 Controller 或 Displayer 结尾，如果起到管理作用可以使用 control 结尾。


**动机**
> 把 model 名放在首位（如 TagUserLikedListController 而不是 UserLikedTagListController）的主要考量是便于搜索。因为 Xcode 不支持乱序搜索，关键词只能从前往后才会有结果。
>
> 如果限定词在前，因为不同人理解差异，自己也会遗忘，这个限定词经常是输入不能的，只能搜 TagList 再从列表中查找，等于第一位的查找语素就废掉了。当 model 类型在第一位时，基本上熟悉这个项目的人都清楚要查找的视图显示的是什么类型，第一位正确了，后面添加/修改限定就很方便了。
> 
> 另一个便利的场景是参考之前界面实现另一个界面时，查找的大都是相同类型的界面，如实现 UserFollowerListController 参考 UserFollowingListController；而相同限定的场景比较少见，像 UserLikedTagListController 参考 UserLikedCategoryListController 的可能性就较少。
>
> PS: 如果你不用 Xcode 的 Open Quickly（默认快捷键 Command+Shift+O），强烈建议你改一下习惯
>

### <a name='naming-resource'></a>资源命名
```Objective-C
实例
icon_eyes【eyes 眼睛 图标的含义】
icon_smile_face【eyes 眼睛 图标的含义】
icon_weibo_share //分享
icon_wechat_share
icon_qq_share
icon_weibo_login //登录
icon_wechat_login
icon_qq_login

//logo
logo_about@2x.png
//背景图片
bg_login@2x.png
bg_regist@2x.png

//默认图
deault_hotplace
deault_houselist
deault_housedetailheader

//箭头
icon_arrowup
icon_arrowdown
icon_arrowleft
icon_arrowright
```




### <a name='naming-method'></a>函数命名

* 以 `alloc`、`copy`、`init`、`mutableCopy`、`new` 开头的方法要注意，它们会改变ARC的行为。[^1]
* 以 `get`、`set` 开头的方法有特殊的意义，不要随意定义。
  1. set 是属性默认的设置方法，如果函数不是为了设置类成员，则不要用 `set` 开头，可用 `setup` 替代。
  2. get 和属性方法无关，但在 Cocoa 中，其标准行为是通过引用传值，而不是直接返回结果的。欲获取变量，直接以变量名为名，如：`userInfomation`，而不是 `getUserInfomation`。

例：

```Objective-C
// OK
- (NSString *)name;

// 糟糕，应用上面的写法
- (NSString *)getName;

// OK，但极少使用
- (void)getName:(NSString **)buffer range:(NSRange)inRange;


// OK
- (NSSize)cellSize;

// 糟糕，应用上面的写法
- (NSSize)calcCellSize;
 

// 对 controller 做一般设置，OK
- (void)setupController;

// 列出具体设置的内容，更好
- (void)setupControllerObservers;

// 糟糕，set 专用于设置属性
- (void)setController;
```

对于有多个参数的方法，务必在每一个参数前都添加关键词，关键词应当清晰说明参数的作用：  

```Objective-C
//正确，保证每个参数都有关键词修饰
- (void)sendAction:(SEL)aSelector toObject:(id)anObject forAllCells:(BOOL)flag;

//错误，遗漏关键词
- (void)sendAction:(SEL)aSelector :(id)anObject :(BOOL)flag;

//正确
- (id)viewWithTag:(NSInteger)aTag;

//错误，关键词的作用不清晰
- (id)taggedView:(int)aTag;
```
不要用and来连接两个参数，通常and用来表示方法执行了两个相对独立的操作（从设计上来说，这时候应该拆分成两个独立的方法）：

```Objective-C
//错误，不要使用"and"来连接参数
- (int)runModalForDirectory:(NSString *)path andFile:(NSString *)name andTypes:(NSArray *)fileTypes;

//正确，使用"and"来表示两个相对独立的操作
- (BOOL)openFile:(NSString *)fullPath withApplication:(NSString *)appName andDeactivate:(BOOL)flag;
```
方法表示让对象执行一个动作，使用动词打头来命名，注意不要使用do，does这种多余的关键字，动词本身的暗示就足够了

```Objective-C
//动词打头的方法表示让对象执行一个动作
- (void)invokeWithTarget:(id)target;
- (void)selectTabViewItem:(NSTabViewItem *)tabViewItem;
```

#### <a name='naming-method-parmas'></a>参数命名

方法的参数命名也有一些需要注意的地方:

* 和方法名类似，参数的第一个字母小写，后面的每一个单词首字母大写
* 不要再方法名中使用类似`pointer`,`ptr`这样的字眼去表示指针，参数本身的类型足以说明
* 不要使用只有一两个字母的参数名
* 不要使用简写，拼出完整的单词

```Objective-C
...action:(SEL)aSelector
...alignment:(int)mode
...atIndex:(int)index
...content:(NSRect)aRect
...doubleValue:(double)aDouble
...floatValue:(float)aFloat
...font:(NSFont *)fontObj
...frame:(NSRect)frameRect
...intValue:(int)anInt
...keyEquivalent:(NSString *)charCode
...length:(int)numBytes
...point:(NSPoint)aPoint
...stringValue:(NSString *)aString
...tag:(int)anInt
...target:(id)anObject
...title:(NSString *)aString
```
#### <a name='naming-method-accessor'></a>存取函数命名
存取方法是指用来获取和设置类属性值的方法，属性的不同类型，对应着不同的存取方法规范：

```Objective-C
//属性是一个名词时的存取方法范式
- (type)noun;
- (void)setNoun:(type)aNoun;
//栗子
- (NSString *)title;
- (void)setTitle:(NSString *)aTitle;

//属性是一个形容词时存取方法的范式
- (BOOL)isAdjective;
- (void)setAdjective:(BOOL)flag;
//栗子
- (BOOL)isEditable;
- (void)setEditable:(BOOL)flag;

//属性是一个动词时存取方法的范式
- (BOOL)verbObject;
- (void)setVerbObject:(BOOL)flag;
//栗子
- (BOOL)showsAlpha;
- (void)setShowsAlpha:(BOOL)flag;
```
命名存取方法时不要将动词转化为被动形式来使用：

```Objective-C
//正确
- (void)setAcceptsGlyphInfo:(BOOL)flag;
- (BOOL)acceptsGlyphInfo;

//错误，不要使用动词的被动形式
- (void)setGlyphInfoAccepted:(BOOL)flag;
- (BOOL)glyphInfoAccepted;
```

可以使用`can`,`should`,`will`等词来协助表达存取方法的意思，但不要使用`do`,和`does`：

```Objective-C
//正确
- (void)setCanHide:(BOOL)flag;
- (BOOL)canHide;
- (void)setShouldCloseDocument:(BOOL)flag;
- (BOOL)shouldCloseDocument;

//错误，不要使用"do"或者"does"
- (void)setDoesAcceptGlyphInfo:(BOOL)flag;
- (BOOL)doesAcceptGlyphInfo;
```

为什么Objective-C中不适用`get`前缀来表示属性获取方法？因为`get`在Objective-C中通常只用来表示从函数指针返回值的函数：

```Objective-C
//三个参数都是作为函数的返回值来使用的，这样的函数名可以使用"get"前缀
- (void)getLineDash:(float *)pattern count:(int *)count phase:(float *)phase;
```


#### <a name='naming-method-delegate'></a>委托函数命名
当特定的事件发生时，对象会触发它注册的委托方法。委托是Objective-C中常用的传递消息的方式。委托有它固定的命名范式。

一个委托方法的第一个参数是触发它的对象，第一个关键词是触发对象的类名，除非委托方法只有一个名为`sender`的参数：

```Objective-C
//第一个关键词为触发委托的类名
- (BOOL)tableView:(NSTableView *)tableView shouldSelectRow:(int)row;
- (BOOL)application:(NSApplication *)sender openFile:(NSString *)filename;

//当只有一个"sender"参数时可以省略类名
- (BOOL)applicationOpenUntitledFile:(NSApplication *)sender;
```

根据委托方法触发的时机和目的，使用`should`,`will`,`did`等关键词

```Objective-C
- (void)browserDidScroll:(NSBrowser *)sender;

- (NSUndoManager *)windowWillReturnUndoManager:(NSWindow *)window;、

- (BOOL)windowShouldClose:(id)sender;
```

#### <a name='naming-method-collection'></a>集合操作命名

有些对象管理着一系列其它对象或者元素的集合，需要使用类似“增删查改”的方法来对集合进行操作，这些方法的命名范式一般为：

```Objective-C
//集合操作范式
- (void)addElement:(elementType)anObj;
- (void)removeElement:(elementType)anObj;
- (NSArray *)elements;

//栗子
- (void)addLayoutManager:(NSLayoutManager *)obj;
- (void)removeLayoutManager:(NSLayoutManager *)obj;
- (NSArray *)layoutManagers;
```

注意，如果返回的集合是无序的，使用`NSSet`来代替`NSArray`。如果需要将元素插入到特定的位置，使用类似于这样的命名：

```Objective-C
- (void)insertLayoutManager:(NSLayoutManager *)obj atIndex:(int)index;
- (void)removeLayoutManagerAtIndex:(int)index;
```

如果管理的集合元素中有指向管理对象的指针，要设置成`weak`类型以防止引用循环。

下面是SDK中`NSWindow`类的集合操作方法：

```Objective-C
- (void)addChildWindow:(NSWindow *)childWin ordered:(NSWindowOrderingMode)place;
- (void)removeChildWindow:(NSWindow *)childWin;
- (NSArray *)childWindows;
- (NSWindow *)parentWindow;
- (void)setParentWindow:(NSWindow *)window;
```
### <a name='naming-properties'></a>属性命名
属性和对象的存取方法相关联，属性的第一个字母小写，后续单词首字母大写，不必添加前缀。属性按功能命名成名词或者动词：

```Objective-C
//名词属性
@property (strong) NSString *title;

//动词属性
@property (assign) BOOL showsAlpha;
```

属性也可以命名成形容词，这时候通常会指定一个带有`is`前缀的get方法来提高可读性：

```Objective-C
@property (assign, getter=isEditable) BOOL editable;
```
*按苹果的说法，不建议在除了`init`和`dealloc`方法以外的地方直接访问实例变量，但很多人认为直接访问会让代码更加清晰可读，只在需要计算或者执行操作的时候才使用存取方法访问，我就是这种习惯，所以这里不作要求。*


### <a name='naming-protocol'></a>协议名
好的协议名应能立刻让人分辨出这不是一个类名，除了以常用的 delegate、dateSource 做结尾外，还可以使用 …ing 这种形式，如：`NSCoding`、`NSCopying`、`NSLocking`。


### <a name='naming-notifications'></a>通知命名
基本命名格式是：`[与通知相关的类名] + [Did | Will] + [UniquePartOfName] + Notification`，例：

```Objective-C
NSApplicationDidBecomeActiveNotification
NSWindowDidMiniaturizeNotification
NSTextViewDidChangeSelectionNotification
NSColorPanelColorDidChangeNotification
```

### <a name='naming-temporary-variable'></a>临时变量命名
* 临时变量可以写得很短，如 i、k、vc 这样；
* 临时变量可以使用匈牙利前缀，但数据类型不可以作为前缀：

```C
// OK
wCell, vcMaster, vToolbar

// 糟糕，数据类型作为前缀
bool_switchState, floatBoxHeight
```

推荐的前缀：

前缀 | 含义
-----|-----
ix   | 序号，起始为0
in   | 序号（自然数范围），起始为1
if   | 类型为浮点的“序号”
x    | 坐标
y    | 坐标
w    | 宽度
h    | 高度
vc   | 视图控制器
v    | 视图

### <a name='naming-constant'></a>常量命名
为私有方法命名不要直接以“_”开头，而应以“命名空间_”开头。

如果要定义一组相关的常量，尽量使用枚举类型（enumerations），枚举类型的命名规则和函数的命名规则相同。
建议使用 `NS_ENUM` 和 `NS_OPTIONS` 宏来定义枚举类型，参见官方的 [Adopting Modern Objective-C](https://developer.apple.com/library/ios/releasenotes/ObjectiveC/ModernizationObjC/AdoptingModernObjective-C/AdoptingModernObjective-C.html) 一文：

```Objective-C
//定义一个枚举
typedef NS_ENUM(NSInteger, NSMatrixMode) {
    NSRadioModeMatrix,
    NSHighlightModeMatrix,
    NSListModeMatrix,
    NSTrackModeMatrix
};
```

定义bit map：

```Objective-C
typedef NS_OPTIONS(NSUInteger, NSWindowMask) {
    NSBorderlessWindowMask      = 0,
    NSTitledWindowMask          = 1 << 0,
    NSClosableWindowMask        = 1 << 1,
    NSMiniaturizableWindowMask  = 1 << 2,
    NSResizableWindowMask       = 1 << 3
};
```

使用`const`定义浮点型或者单个的整数型常量，如果要定义一组相关的整数常量，应该优先使用枚举。常量的命名规范和函数相同：

```Objective-C
const float NSLightGray;
```

不要使用`#define`宏来定义常量，如果是整型常量，尽量使用枚举，浮点型常量，使用`const`定义。`#define`通常用来给编译器决定是否编译某块代码，比如常用的：

```Objective-C
#ifdef DEBUG
```

注意到一般由编译器定义的宏会在前后都有一个`__`，比如*`__MACH__`*。
#### <a name='naming-others'></a>其他
i，j专用于循环标号
