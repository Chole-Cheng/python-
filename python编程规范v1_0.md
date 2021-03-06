# 大数据组 Python V1.0 编程规范

## 背景介绍

### 简介

Python 编程规范主要为了方便实验代码的阅读和管理，同时便于日常调试和维护，在研究课题完成后相关代码需要按照编程规范进行归档。

### 参考资料

* [Google Python风格指南](https://zh-google-styleguide.readthedocs.io/en/latest/)
* [jupyter notebook可以做什么](https://www.zhihu.com/question/46309360/answer/254638807)

## 编程风格及规范

### 导入

导入仅对包和模块使用，使得模块间共享代码。使用导入使得命名空间管理约定十分简单；缺点在于模块名仍可能冲突、部分模块名太长。  

**Recommended** 

* 使用`import x`来导入包和模块。

* 使用`from x import y`, x是包前缀，y是不带前缀的模块名。

* 使用```from x import y as z```, 用于两个将要导入的模块重名或导入的模块y名称太长。

  例如，用如下方式导入长名称模块```sound.effects.echo```:

  ```python
  from sound.effects import echo
  ...
  echo.EchoFilter(input, output, delay=0.7, atten=4)
  ```

* 每个导入应占一行

  ```python
  Yes: import os
       import sys
  ```
  
* 导入总应该放在文件顶部，位于模块注释和文档字符串之后，模块全局变量和常量之前。导入应该按照从最通用到最不通用的顺序分组：

  1. 标准库导入

  2. 第三方库导入

  3. 应用程序指定导入

   在每种分组中, 应该根据每个模块的完整包路径按字典序排序, 忽略大小写。
  
  ```python
  import foo
  from foo import bar
  from foo.bar import baz
  from foo.bar import Quux
  from Foob import ar
  ```

**Not Recommended**

* 导入包时不应使用相对名称。即使模块在同一个包中, 也应使用完整包名，以避免无意间将一个包导入两次。

* 导入多个包时，不应同占一行

  ```python
  No:  import os, sys
  ```

### 列表推导

列表推导和生成器表达式提供了一种简洁高效的方式来创建列表和迭代器。不必借助map(), filter(), 或lambda(), 比其它的列表创建方法更加清晰简单。因为避免了创建整个列表，生成器表达式可以十分高效；缺点是复杂的列表推导或者生成器表达式可能难以阅读。  

**Recommended** 

* 对于简单情况，每个部分应该单独置于一行: 映射表达式, for语句, 过滤器表达式。

  ```python
  Yes:  
    return ((x, complicated_transform(x))
            for x in long_generator_function(parameter)
            if x is not None)
  
    squares = [x * x for x in range(10)]
  
    eat(jelly_bean for jelly_bean in jelly_beans
        if jelly_bean.color == 'black')
  ```

**Not Recommended**

* 禁止多重for语句或过滤器表达式，复杂情况下应选择使用循环。

  ```python
  No:
    result = [(x, y) for x in range(10) for y in range(5) if x * y > 10]
  
    return ((x, y, z)
            for x in xrange(5)
            for y in xrange(5)
            if x != y
            for z in xrange(5)
            if y != z)
  ```

### Lambda 函数

Lambda在一个表达式中定义匿名函数，与语句相反。常用于为 `map()` 和 `filter()` 之类的高阶函数定义回调函数或者操作符。  

优点是使用方便；由于通常只包含一个表达式，表达能力有限，比本地函数更难阅读和调试。没有函数名意味着堆栈跟踪更难理解。

**Recommended**

* 适用于单行函数，如果代码超过60-80个字符，最好定义成常规(嵌套)函数。

* 对于常见的操作符，使用模块函数代替Lambda函数。

  例如乘法操作符，使用 `operator` 模块中的函数`operator.mul`以代替lambda函数 `lambda x, y: x * y` 。

**Not Recommended**

* 不适用与非单行函数，代码超过60-80个字符，不应使用Lambda函数，推荐定义成常规(嵌套)函数。

### 条件表达式

条件表达式是对于if语句的一种更为简短的句法规则。 例如: `x = 1 if cond else 2` 。比if语句更加简短和方便，但如果表达式很长，难于定位条件，会比if语句难于阅读。

**Recommended**

* 用于单行函数。

**Not Recommended**

* 不适用于非单行函数，推荐使用完整的if语句。

### 默认参数值

在函数参数列表的最后指定变量的值，例如 `def foo(a, b=0):` 。如果调用foo时只带一个参数, 则b被设为0. 如果带两个参数, 则b的值等于第二个参数。

默认参数值提供了一种简单的方法覆盖使用大量默认值函数的默认值, python不支持重载方法和函数，默认参数是一种“伪造”重载行为的简单方式。但是默认参数只在模块加载时求值一次，如果参数是列表或字典之类的可变类型, 这可能会导致问题；如果函数修改了对象(例如向列表追加项), 默认值将被修改。

**Recommended**

* 适用于大部分情况，鼓励使用。
```python
Yes: def foo(a, b=None):
         if b is None:
             b = []
```

**Not Recommended**

* 不要在函数或方法定义中使用可变对象作为默认值。

```python
No:  def foo(a, b=[]):
         ...
No:  def foo(a, b=time.time()):  # The time the module was loaded???
         ...
No:  def foo(a, b=FLAGS.my_thing):  # sys.argv has not yet been parsed...
         ...
```

### True/False

Python布尔值的条件语句更易读且不易犯错，大部分情况下**速度更快**。Python在布尔上下文中会将某些值求值为False。例如，所有的”空”值0，None，[]，{}都被认为是False。

**Recommended**

* 尽可能使用隐式False，例如: 使用 `if foo:` 而不是 `if foo != []:`。
*  `if x:` 等价于`if x is not None:`。当要测试一个默认值是None的变量或参数是否被设为其它值. 这个值在布尔语义下可能是False!
* 对于序列(字符串, 列表, 元组)，注意空序列是False. 因此 `if not seq:` 或者 `if seq:` 比 `if len(seq):` 或 `if not len(seq):` 更好。
* 处理整数时, 使用隐式False可能会不小心将None当做0来处理。可以将一个已知是整型(且不是函数`len()`的返回结果)的值与0比较。
* 注意‘0’(字符串)会被当做True。

```python
Yes: if not users:
         print 'no users'

     if foo == 0:
         self.handle_zero()

     if i % 10 == 0:
         self.handle_multiple_of_ten()
```

**Not Recommended**

* 不应使用==或者!=来比较单件， 比如None。使用is或者is not。
* 不应使用==将一个布尔量与False相比较。使用 `if not x:` 代替。如果你需要区分False和None， 应使用 `if not x and x is not None:` 这样的语句。

```python
No:  if len(users) == 0:
         print 'no users'

     if foo is not None and not foo:
         self.handle_zero()

     if not i % 10:
         self.handle_multiple_of_ten()
```

### 空格

按照标准的排版规范来使用空格。

**Recommended AND Not Recommended**

* 括号内不要有空格：

  ```python
  Yes: spam(ham[1], {eggs: 2}, [])
  
  No: spam( ham[ 1 ], { eggs: 2 }, [ ] )
  ```
  
* 不要在逗号, 分号, 冒号前面加空格, 但应该在它们后面加(除了在行尾)：

  ```python
  Yes: if x == 4:
           print x, y
       x, y = y, x
      
  No:  if x == 4 :
           print x , y
       x , y = y , x
  ```

* 参数列表, 索引或切片的左括号前不应加空格：

  ```python
  Yes: spam(1)
       
       dict['key'] = list[index]
      
      
  No: spam (1)
      
      dict ['key'] = list [index]
  ```

* 在二元操作符两边都加上一个空格, 比如赋值(=)， 比较(==, <, >, !=, <>, <=, >=, in, not in, is, is not)，布尔(and, or, not)；

  算术操作符两边的空格该如何使用, 需要你自己好好判断。不过两侧务必要保持一致：
  
  ```python
  Yes: x == 1
     
  No:  x<1
  ```
  
* 当’=’用于指示关键字参数或默认参数值时, 不要在其两侧使用空格：

  ```python
  Yes: def complex(real, imag=0.0): return magic(r=real, i=imag)
      
  No:  def complex(real, imag = 0.0): return magic(r = real, i = imag)
  ```

* 不要用空格来垂直对齐多行间的标记, 因为这会成为维护的负担(适用于:, #, =等)：

  ```python
  Yes:
       foo = 1000  # comment
       long_name = 2  # comment that should not be aligned
  
  No:
       foo       = 1000  # comment
       long_name = 2     # comment that should not be aligned
  ```

### 换行
除了长的导入模块语句or注释里的URL，每行不超过80个字符。换行时如需要，使用4个空格来缩进代码，绝对不要使用tab，也不要tab和空格混用。

**Recommended** 

* 顶级定义之间空两行，比如函数或者类定义
* 方法定义、类定义与第一个方法之间，应空一行；函数或方法中，某些地方觉得合适可空一行
* 不要使用反斜杠连接行，Python会将圆括号，中括号和花括号中的行隐式的连接起来。如需要，可在表达式外围增加一对额外的圆括号

  ```python
  Yes: foo_bar(self, width, height, color='black', design=None, x='foo',
               emphasis=None, highlight=0)

       if (width == 0 and height == 0 and
           color == 'red' and emphasis == 'strong'):
  ```
  
* 如果一个文本字符串在一行放不下, 可以使用圆括号来实现隐式行连接

  ```python
  Yes: x = ('This will build a very long long '
            'long long long long long long string')
  ```
  
* 注释中，将长的URL放在一行上

 ```python
 Yes:  # See details at
       # http://www.example.com/us/developer/documentation/api/content/v2.0/csv_file_name_extension_full_specification.html
  ```
  
* 行连接的情况，应垂直对齐换行的元素，或者使用4空格的悬挂式缩进(这时第一行不应该有参数)

  ```python
  Yes:   # Aligned with opening delimiter
         foo = long_function_name(var_one, var_two,
                                  var_three, var_four)

         # Aligned with opening delimiter in a dictionary
         foo = {
             long_dictionary_key: value1 +
                                  value2,
             ...
         }

         # 4-space hanging indent; nothing on first line
         foo = long_function_name(
             var_one, var_two, var_three,
             var_four)

         # 4-space hanging indent in a dictionary
         foo = {
             long_dictionary_key:
                 long_dictionary_value,
             ...
         }
  ```

**Not Recommended**
* 注释中，URL不建议换行，应放在一行上

  ```python
  Yes:  # See details at
        # http://www.example.com/us/developer/documentation/api/content/\
        # v2.0/csv_file_name_extension_full_specification.html
  ```
  
* 行连接的情况，换行时禁止不采用缩进、采用2空格缩进或在字典中进行不恰当缩进

  ```python
  No: # Stuff on first line forbidden
      foo = long_function_name(var_one, var_two,
         var_three, var_four)

      # 2-space hanging indent forbidden
      foo = long_function_name(
        var_one, var_two, var_three,
        var_four)

      # No hanging indent in a dictionary
      foo = {
          long_dictionary_key:
              long_dictionary_value,
              ...
      }
  ```

### 命名

**命名约定**

1. 内部(Internal)表示仅模块内可用, 或者在类内是保护或私有的
2. 用单下划线(\_)开头表示模块变量或函数是protected的(使用from module import \* 时不会包含)
3. 用双下划线(\_\_)开头的实例变量或方法表示类内私有
4. 可以将相关的类和顶级函数放在同一个模块里，没必要像Java一样限制一个类一个模块
5. 对类名使用大写字母开头的单词(如CapWords)，模块名使用小写加下划线的方式(如lower_with_under.py)；有很多现存的模块使用类似于CapWords.py这样的命名, 但现在已经不鼓励这样做, 因为如果模块名碰巧和类名一致, 会让人困扰

**Recommended**

|**Type**|**Public**|**Internal**|
|-----|-----|-----|
|Modules|lower_with_under|\_lower\_with\_under|
|Packages|lower_with_under||
|Classes|CapWords|\_CapWords|
|Exceptions|CapWords||
|Functions|lower_with_under()|\_lower_with_under|
|Global/Class Constants|CAPS_WITH_UNDER|\_CAPS_WITH_UNDER|
|Global/Class Variables|lower_with_under|\_lower_with_under|
|Instance Variables|lower_with_under|\_lower_with_under(protected)or\_\_lower_with_under(private)|
|Method Names|lower_with_under()|\_lower_with_under(protected)or\_\_lower_with_under(private)|
|Function/Method Parameters|lower_with_under||
|Local Variables|lower_with_under||

**Not Recommended**

* 避免单字符名称，除了计数器和迭代器
* 避免包/模块名中的连字符(-)
* 避免双下划线开头并结尾的名称(Python保留，例如\_\_init\_\_)

## 注释规范

确保对模块，函数，方法和行内注释使用正确的风格。

### 块注释

可以使用以“#”注释的多个单行注释，也可以使用3个单引号开头和结尾一次性注释多行内容，注释应在操作开始前。

```python
 '''
 We use a weighted dictionary search to find out where i is in 
   the array.  We extrapolate position based on the largest num 
   in the array and the array size and then do binary search to 
   get the exact number.
   ...
 '''
 
 if i & (i-1) == 0:
```

```python
# We use a weighted dictionary search to find out where i is in
# the array.  We extrapolate position based on the largest num
# in the array and the array size and then do binary search to
# get the exact number.

if i & (i-1) == 0:
```


### 行注释

单行注释使用“#”作为注释符号，从符号开始处，直到换行结束，此部分为注释内容。放置位置可以是要注释代码的前一行，也可是代码的行尾。

**Recommended**

* 最需要写注释的是代码中技巧性的部分。对于复杂的操作, 应该在其操作开始前写上若干行注释；对于不是一目了然的代码, 应在其行尾添加注释
* 行尾注释，注释应至少离开代码2个空格以提高可读性

```python
# We use a weighted dictionary search to find out where i is in
# the array.  We extrapolate position based on the largest num
# in the array and the array size and then do binary search to
# get the exact number.

if i & (i-1) == 0:        # True if i is 0 or a power of 2.
```

**Not Recommended**

* 绝对不要描述代码，应假设阅读代码的人比你更懂Python，只是不知道你的代码要做什么

  ```python
  No：
  # BAD COMMENT: Now go through the b array and make sure whenever i occurs
  # the next element is i+1
  ```

### docstrings

文档字符串为多行注释，常用来为python文件、模块、类或函数等添加版权、功能描述等信息，是包、模块、类或函数里的第一个语句，这些字符串可以通过对象的\_\_doc\_\_成员被自动提取, 并且被pydoc所用。

**Recommended**

**文档字符串格式：文档字符串的惯例是使用三重双引号"""( PEP-257 )； 首行是以句号、问号或惊叹号结尾的概述(或该文档字符串单纯只有一行)；接着是一个空行；然后是文档字符串剩下的部分，它应该与文档字符串的第一行的第一个引号对齐。**

#### 模块注释

* 每个文件应该包含一个许可样板。根据项目使用的许可(例如：Apache 2.0，BSD，LGPL，GPL)，选择合适的样板。

#### 函数和方法注释

下文所指的函数，包括函数、方法以及生成器。

* 一个函数必须要有文档字符串, 除非它满足以下条件:
  * 外部不可见
  * 非常短小
  * 简单明了

* 文档字符串应该包含函数做什么，输入和输出的详细描述。
  通常，不应该描述“怎么做”，除非是一些复杂的算法。文档字符串应该提供足够的信息，当别人编写代码调用该函数时，不需要看一行代码, 只要看文档字符串就可以了；然而对于复杂的代码，在代码旁边加注释会比使用文档字符串更有意义。

* 关于函数的几个方面应该在特定的小节中进行描述记录；每节应该以一个标题行开始，标题行以冒号结尾；除标题行外，节的其他内容应被缩进2个空格。

  **Args:**

  列出每个参数的名字，并在名字后使用一个冒号和一个空格，分隔对该参数的描述。如果描述太长超过了单行80字符,使用2或者4个空格的悬挂缩进(与文件其他部分保持一致)。描述应该包括所需的类型和含义。如果一  个函数接受\*foo(可变长度参数列表)或者\*\*bar(任意关键字参数)，应该详细列出\*foo和\*\*bar。

  **Returns：**(或Yields：用于生成器)

  描述返回值的类型和语义。如果函数返回None，这一部分可以省略。

  **Raises:**
  列出与接口有关的所有异常。

```python
def fetch_bigtable_rows(big_table, keys, other_silly_variable=None):
    """Fetches rows from a Bigtable.

    Retrieves rows pertaining to the given keys from the Table instance
    represented by big_table.  Silly things may happen if
    other_silly_variable is not None.

    Args:
        big_table: An open Bigtable Table instance.
        keys: A sequence of strings representing the key of each table row
            to fetch.
        other_silly_variable: Another optional variable, that has a much
            longer name than the other args, and which does nothing.

    Returns:
        A dict mapping keys to the corresponding table row data
        fetched. Each row is represented as a tuple of strings. For
        example:

        {'Serak': ('Rigel VII', 'Preparer'),
         'Zim': ('Irk', 'Invader'),
         'Lrrr': ('Omicron Persei 8', 'Emperor')}

        If a key from the keys argument is missing from the dictionary,
        then that row was not found in the table.

    Raises:
        IOError: An error occurred accessing the bigtable.Table object.
    """
    pass
```

#### 类注释

* 类应该在其定义下有一个用于描述该类的文档字符串。如果类有公共属性(Attributes)，那么文档中应该有一个属性(Attributes)段并且遵守和函数参数相同的格式。

```python
class SampleClass(object):
    """Summary of class here.

    Longer class information....
    Longer class information....

    Attributes:
        likes_spam: A boolean indicating if we like SPAM or not.
        eggs: An integer count of the eggs we have laid.
    """

    def __init__(self, likes_spam=False):
        """Inits SampleClass with blah."""
        self.likes_spam = likes_spam
        self.eggs = 0

    def public_method(self):
        """Performs operation blah."""
```

### TODO

为临时代码使用TODO注释，一种短期解决方案。统一的格式使添加注释的人就可以搜索到并可以按需提供更多细节，写了TODO注释并不保证写的人会亲自解决问题，当你写TODO时，需要注上你的名字。

**Recommended**

* TODO统一的格式：TODO注释应该在所有开头处包含“TODO”字符串，紧跟着是用括号括起来的你的名字，email地址或其它标识符；然后是一个可选的冒号，接着必须有一行注释，解释要做什么。

  ```python
  # TODO(kl@gmail.com): Use a "*" here for string repetition.
  # TODO(Zeke) Change this to use relations.
  ```
  
* 如果TODO是“将来做某事”的形式，需确保包含了一个指定的日期(“2009年11月解决”)或者一个特定的事件(“等到所有的客户都可以处理XML请求就移除这些代码”)


## 编程建议

### __future__模块

python在新版本中引入的功能有时候是不兼容旧版本的，通过__future__模块，python2可以调用python3的某些功能，例如print函数，__future__模块可以使得python2.x和python3.x有更好的兼容性。但是现在由于python2.x不再维护，因此建议使用python3.6，如果存在部分开源代码使用python2.x的情况，可以通过__future__模块更方便的融入到自己的工程中。

### isinstance

python的内置函数，用于判断对象的类型是否和已知类型相同时，推荐使用isinstance函数而不是type函数，isinstance会认为子类是一种父类型，考虑继承关系，而type不会认为子类是一种父类型，不考虑继承关系。

```python
class A:
	pass
	
class B(A):
	pass
	
isinstance(A(), A) # returns True
type(A()) == A # returns True
isinstance(B(), A) # returns True，考虑继承关系
type(B()) == A # returns False，不考虑继承关系
```

### 文件读取

文件读取是python的基本IO操作，一般通过open获取文件句柄然后读取文件，但是当文件读取出错时获取具柄异常，文件将无法关闭，特别是在我们读取大文件的时候文件无法关闭意味着占用的内存无法得到释放，只能重新启动程序或者手动处理。因此我们建议通过with open实现文件读取，with open的好处就是程序到达语句末尾的时候即使出现异常也会自动关闭文件。

**Not Recommended**

```python
f = open("file.txt", "r")
content = f.readlines()
f.close()
```

**Recommended**

```python
with open("file.txt", "r") as f:
	content = f.readlines()
```

## 编程助手

### Jupyter Notebook

> Jupyter notebook（http://jupyter.org/） 是一种 Web 应用，能让用户将说明文本、数学方程、代码和可视化内容全部组合到一个易于共享的文档中。

Jupyter notebook支持的功能：

* 交互式调试
* Markdown
* 多语言
* 命令行
* 主题切换
* 丰富的插件
* ......

jupyter notebook中一个非常重要的概念就是cell，每个cell能够单独进行运算，这样适合于代码调试。我们开发一个完整的脚本时变量会随着代码执行的结束而从内存中释放，如果我们想看中间的变量或者结构，我们只能通过断点或者输出日志信息的方式进行调试，这样无疑是非常繁琐的，如果一个程序运行很多这种方式还可行，如果运行时间长达几个小时，这样我们调试一圈耗费的时间就太长了。

而在jupyter notebook中我们可以把代码分隔到不同的cell里逐个进行调试，这样它会持续化变量的值，我们可以交互式的在不同cell里获取到我们想要测试的变量值和类型。在机器学习或者深度学习的研究项目中，我们需要快速的试错和尝试不同的方法，jupyter notebook就成为了一个非常好的工具，可以帮助我们可视化分析数据和结果，配合交互式的功能也可以充当PPT的角色用于做成果展示。

Jupyter notebook部分有用的功能：

* [魔术函数](https://www.sohu.com/a/388381278_114774)
* [操作快捷键](https://www.jianshu.com/p/72493e81a708)
* [实用扩展插件](https://zhuanlan.zhihu.com/p/97394628)
* [基于matplotlib的可交互图表seaborn](https://seaborn.pydata.org/introduction.html)

### Pycharm

待补充

### Colab

待补充

### pdb调试

待补充

## 服务器

* 二号基地服务器(2ai5229178.wicp.vip:42620), 本科生需向实验室老师和师兄(袁老师、张永老师、程骋、马贵君)报备后使用，在Document文件夹下建立自己的文件夹并做好数据保密和文档命名分类.
* 启明学院服务器(24677m13k6.wicp.vip:45306)，仅硕士和博士生可用。

项目、课题结束和毕业前，需整理所有程序和数据文档，统一拷贝给马贵君师兄存储。

## 修订记录

* 2020年7月27日，由王雪创建了文档
* 2020年8月2日，由周倍同重新修改和添加了部分目录结构
* 2020年8月3日，由王雪补充了文档内容
