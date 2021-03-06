《基于网络爬虫的漏洞检测系统》
====
# 1.1系统功能介绍 
	
&#8195;&#8195;该系统的主要功能有网站网络爬虫、漏洞测试、漏洞验证、生成报告、发送报告到指定邮箱、使用帮助、常见漏洞学习、扫描排错、扫描历史浏览等。\
&#8195;&#8195;网络爬虫模块是该系统第一个模块，这个模块对于该系统进行漏洞检测至关重要，因为其他模块都是建立在这个模块之上。\
&#8195;&#8195;这个模块的效率影响着整个漏洞检测的总体时间，不仅仅是因为它是该系统的第一个模块，还是因为漏洞验证在发包时用的也是这个模块，在漏洞爬取策略中，讨论了使用的爬取策略算法，因为深度优先算法不好实现加使用驻留内存大等缺点，所以该系统采用广度优先算法进行爬取。\
&#8195;&#8195;在爬虫爬取的过程中也要对爬到的网址进行分析，分析爬到的网址是否属于该域名，如果不属于指定网址的域名，则剔除，如果属于该域名，则加入待爬队列，直到爬取队列没有网址为止。同时将爬取网址同时放到另一个集合内，等待分析。\
&#8195;&#8195;URL解析中利用正则表达式匹配出主域名，之后进行网址爬取的时候，就以这个主域名为限制，不属于主域名的网址删掉，在网页下载用HttpWEBResponse方法进行接收，接收下来的网页进行分析，解析漏洞可疑点分为两种，一种可疑点是在URL中，一种在HTML中，注入点可能存在URL中，也可能在Form表单中，所以要对这两个可疑点进行分析，将提取的信息和URL进行保存，以待Payload生成使用。\
&#8195;&#8195;XSS Payload生成使用多线程，XSS Payload模块框架图如图1-2所示，在爬虫模块中提取的可疑漏洞点在这就用到了。\
&#8195;&#8195;先根据可疑点分析注入点类型，注入点类型分为GET和POST，一般通过URL传参就是GET，表单一般都是用POST传参，在构造 Payload时，URL中的可疑漏洞的分析分为：第一种是直接在后面加攻击语句，第二种是通过正则表达式替换参数的值。表单要对表单数据进行分析，提取表单参数进行赋值，这些值都是攻击语句。攻击语句是收集的常用攻击语句以及它们的变种，包括：标签闭合、随机大小写、特殊字符嵌入、HTML实体编码、BASE64编码、Unicode编码、UTF编码、16进制编码等等。
 <div align=center><img width="655" height="361" src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片1.png"/></div>
                   
<p align="center">图1-2 XSS Payload生成模块总体设计图</p>
&#8195;&#8195;由于检测的漏洞不同，相同漏洞间的产生原理也不同，所以漏洞检测验证模块也并不相同，但大致步骤都相同，都会有Payload发送、漏洞分析和漏洞验证等模块，漏洞检测和验证流程如图1-3所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片2.png"/></div>
<p align="center">图1-3 XSS漏洞测试和漏洞验证流程图</p>
  
&#8195;&#8195;在该系统中检测过程也是通过类似于爬虫模块进行的，将构造的Payload通过数据包发送出去，当然在发送的时候也要考虑到Cookie的问题，因为有些网站需要登陆才能进行爬取，在XSS漏洞检测时，该系统用的方法是，在开始检测前对攻击语句字典进行分析，会根据检测前的语句进行分析、如编码分析、闭合分析、弹窗分析等。\
&#8195;&#8195;将如果分析后的语句写入另一个字典称为漏洞比对字典，当进行返回数据包检测时，通过攻击语句和漏洞比对字典进行一一对比，如果比对出来了，就发送给漏洞验证模块进行下一步验证，漏洞验证模块使用C#调用Selenium+phantomJS框架模拟浏览器，但是这个时候需人工进行验证。在SQL注入模块验证时，如果扫描出数据就记录Payload，这个Payload也需要人工进行验证。  
&#8195;&#8195;扫描报告对于安全人员的收尾和撰写渗透测试报告很重要，在生成报告模块，生成报告模块框架图如1-4所示。首先是在系统上根据扫描出的漏洞进行分析，其次给出系统漏洞评分，最后生成报告。漏洞评分标准参考微软提出的DREAD模型，但是由于该系统只对两个漏洞进行分析，也不知道漏洞扫描出来的漏洞渗透进行的程度，所以该系统只对扫描漏洞数量进行分析。在报告中会记录扫描人员、扫描时间、扫描得分、给出修复建议以及Payload。报告保存分为三种方式，系统查看、本地保存、邮箱保存，系统查看是在系统上查看生成的报告，邮箱保存为将报告发送到指定邮箱  
&#8195;&#8195;漏洞知识库主要针对，对于漏洞不熟悉的使用者，使用者可以通过查看漏洞知识库进行熟悉漏洞知识，还有一个模块为排错模块，如果在使用者遇到问题，可以通过查看此模块进行快速排错。系统使用时可以查看的文档帮助和视频帮助。文档帮助还是使用CHM文件，在CHM文档中为系统的使用流程，而视频帮助是基于网络爬虫的漏洞检测系统使用的一个小例子，可以通过这个小例子让使用者快速上手使用该系统进行漏洞扫描的工作。  
&#8195;&#8195;帮助模块此模块设计的意义在于如果在用户使用期间出现不可解决的问题或者有其他的需求可以联系开发人员进行反馈，以保证项目朝着贴合渗透的方向进行，加上这个模块不仅对系统使用人员有帮助，可以帮助使用人员进行排错，这对于项目的改进属于正反馈，引导项目的发展方向，当检测中断时可以进行排错。

# 1.2系统主界面  
&#8195;&#8195;系统预加载页面，如下图1-4所示。  
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片3.png"/></div>

<p align="center">图1-4系统预加载页面</p>
&#8195;&#8195;基于网络爬虫的漏洞扫描系统的主页面主要完成的是扫描配置和功能导航，如图1-5所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片4.png"/></div>
<p align="center">图1-5 主页面图</p>

&#8195;&#8195;扫描配置模块主要包括：扫描URL设置，如果网站需要登陆要设置Cookie，设置Cookie的步骤为在浏览器登陆网站，点击开发者工具，点击网络，然后刷新网页，在Cookie选项中，将Cookie复制过来，在扫描前还需要进行扫描漏洞类型选择，这个漏洞类型默认为只进行XSS扫描，所以如果要进行SQL漏洞的扫描，要将两个类型都选上。

# 1.3开发环境

开发软件：Visual studio 2012

使用框架：Winform+Devexpress 15.6

操作word插件：Aspose

# 2.1功能模块
&#8195;&#8195;系统功能模块如图2-1所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片5.png"/></div>
<p align="center">图2-1系统功能模块图</p>

# 2.2业务流程
&#8195;&#8195;在使用该系统前，需要对该系统的设计流程做个了解，设计流程图如图2-2所示。开始使用时，可以对漏洞知识进行梳理，由用户指定扫描URL和扫描漏洞类型以及扫描完成报告发送邮箱等设置，还需要查看扫描网站是否需要登陆，如需登陆可以先登陆网站，在浏览器的开发者工具中，可以找到网络选项里的Cookie，获取登陆后的Cookie后，在系统中选择“需要Cookie”选项，输入Cookie的值，浏览器中的Cookie可以复制粘贴，这样也有利于用户操作，然后再进行最后的负荷配置，这个负荷配置是加载Payload语句，在系统中以字典的形式存在，如果需要配置用户精心构造的Payload，可以从自己本地进行加载，开始扫描后判断之前的设置是否是一键式的，然后就可以进行扫描了，扫描后会根据扫描前的配置生成扫描报告和修复建议，如果配置为一键式则可以自动生成报告和发送报告到指定邮箱。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片6.png"/></div>
<p align="center">图2-2 设计流程图</p>

# 2.3系统架构

&#8195;&#8195;系统框架图如图2-3所示
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片7.png"/></div>
<p align="center">图2-3 系统总体框架图</p>

&#8195;&#8195;基于网络爬虫的漏洞检测系统是一个c/s架构的， 其开发语言为C#，界面是利用DevExpress框架搭建大方美观，其主要功能有含七大模块，这七大模块分别为网站爬取，网站可能存在漏洞测试点分析、漏洞测试、漏洞验证、生成报告、发送报告到指定邮箱、使用帮助、常见漏洞学习等模块。基于网络爬虫的漏洞检测系统是面向安全人员的漏洞扫描软件，安全人员可以使用该系统的客户端，对网站进行XSS和SQL注入漏洞进行扫描，然后对网站漏洞进行分析，给出修复建议和生成测试报告，安全人员也可以在扫描XSS和SQL注入漏洞的同时对漏洞相关知识进行熟悉。

&#8195;&#8195;在进行数据库设计时，对是否使用数据库做了一个探讨，使用数据库的好处在于，容易实现数据共享，数据共享包含所有用户同时存取数据库中的数据，也包括用户可以用接口使用数据库，并提供数据共享。可以减少数据的冗余度。 同文件系统相比，由于数据库实现了数据共享，从而避免了每个用户自建文件。减少了大量重复数据，减少了数据冗余，维护了数据的一致性。数据库也可以实现数据的集中管理，有很好的维护性，但是通过对系统的分析，该系统的一个客户端仅提供一个用户使用，不需要考虑用户的数据共享，在扫描过程中产生的数据有很少需要保存到本地，所以也不用考虑到数据过大的问题，使用数据库还有一个缺点就是，如果使用数据库，在客户端的安装中比较麻烦，需要先安装数据库系统，而一般的安全人员有自己的开打环境，如果本机已经存在数据库系统了，还有可能扰乱本机的开发环境的问题，所以该系统不使数据库。

&#8195;&#8195;在系统中还需要使用多线程来解决窗体“假死”。进程是Windows系统中的一个概念，它包含着的资源是运行程序所需要。一个正在运行的应用程序，在操作系统中看作一个进程，一个进程由一个或者多个线程组成。一个进程中的多个线程可以同时运行，进程之间是相对独立的，一个进程无法进行访问另一个进程的数据。在进行Winform程序开发时，往往需要数据的处理，但是如果数据比较大或者处理过程比较复杂，会需要一定的时间，如果在这段时间内界面不能操作或者没有实时更新，会产生程序“假死”，用户以为程序卡死，而导致不好的用户体验。所以在大量数据操作的应用上，需要使用多线程来处理这种情况。

# 2.4系统部署

&#8195;&#8195;在系统进行使用时可以有两种方式进行，一种是在系统的局域网内通过对WEB应用的直接访问进行扫描，这种方式，如图2-4部署图的用户A所示。

<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片8.png"/></div>
<p align="center">图2-4 部署原理图</p>

&#8195;&#8195;用户A通过本地网络对服务器进行扫描，这种方式是一种旁路的方式，部署简单，只需要安装在局域网的一台计算机上，或者将安装有该系统的计算机接入局域网即可进行扫描，这种方式对本地未上线应用和本地上线应用效果相同，第二种方式是通过网络对WEB应用进行扫描，但这时需要WEB服务器解入Internet，即在局域网外部可以对WEB服务器进行访问，如图4-2部署图的用户B所示，这种情况对于未上线的应用无效，未上线的应用需要在本地环境进行测试。

# 2.5漏洞可能点爬取

## 2.5.1网络爬虫模块

&#8195;&#8195;网络爬虫模块是该系统第一个模块，这个模块对于该系统进行漏洞检测至关重要，因为其他模块都是建立在这个模块之上，爬虫流程图如图2-5所示。这个模块的效率影响着整个漏洞检测的总体时间，不仅仅是因为它是该系统的第一个模块，还是因为漏洞验证在发包时用的也是这个模块，在漏洞爬取策略中，讨论了使用的爬取策略算法，因为深度优先算法不好实现加使用驻留内存大等缺点，所以该系统采用广度优先算法进行爬取。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片9.png"/></div>
<p align="center">图2-5 爬虫流程图</p>

&#8195;&#8195;在爬虫爬取的过程中也要对爬到的网址进行分析，分析爬到的网址是否属于该域名，如果不属于指定网址的域名，则剔除，如果属于该域名，则加入待爬队列，直到爬取队列没有网址为止。同时将爬取网址同时放到另一个集合内，等待分析。

&#8195;&#8195;由于检测的漏洞不同，相同漏洞间的产生原理也不同，所以漏洞检测验证模块也并不相同，但大致步骤都相同，都会有Payload发送、漏洞分析和漏洞验证等模块，漏洞检测和验证流程如图2-6所示。在该系统中检测过程也是通过类似于爬虫模块进行的，将构造的Payload通过数据包发送出去，当然在发送的时候也要考虑到Cookie的问题，因为有些网站需要登陆才能进行爬取，在XSS漏洞检测时，该系统用的方法是，在开始检测前对攻击语句字典进行分析，会根据检测前的语句进行分析、如编码分析、闭合分析、弹窗分析等。

<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片10.png"/></div>
<p align="center">图2-6 XSS漏洞测试和漏洞验证流程图</p>

&#8195;&#8195;将如果分析后的语句写入另一个字典称为漏洞比对字典，当进行返回数据包检测时，通过攻击语句和漏洞比对字典进行一一对比，如果比对出来了，就发送给漏洞验证模块进行下一步验证，漏洞验证模块使用C#调用Selenium+phantomJS框架模拟浏览器，但是这个时候需人工进行验证。在SQL注入模块验证时，如果扫描出数据就记录Payload，这个Payload也需要人工进行验证。

&#8195;&#8195;先看反射型XSS的检测，下面是反射型XSS漏洞产生的原理，如图2-7反射型XSS原理图所示。被攻击者登陆到指定网站。攻击者将自己精心准备的URL发送给被攻击者。这一步主要是欺骗被攻击者，让被攻击者点击发送的URL。被攻击者点击并请求这个URL，服务器对被攻击者的请求做出响应。攻击者的脚本在用户的浏览器中执行并向攻击者发送会话。攻击者劫持会话，这样就完成了一次攻击。

&#8195;&#8195;知道反射型XSS漏洞原理了，反射型XSS漏洞检测就清晰了，在XSS漏洞中，反射型的XSS漏洞是最容易检测的，因为反射型XSS漏洞，只需要通过GET方法发送构造的Payload就可以。所以基本检测流程，先发送Payload，然后接收数据包，在对数据包进行分析，解析出脚本，这样反射型XSS漏洞的检测就基本完成了。

<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片11.png"/></div>
<p align="center">图2-7 反射性XSS漏洞原理图</p>

&#8195;&#8195;存储型XSS的原理如图2-8存储型XSS漏洞原理图所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片12.png"/></div>
<p align="center">图2-8 存储型XSS漏洞原理图</p>

&#8195;&#8195;反射型XSS和存储型XSS是按照用户的输入是否存到服务器来区分的，存储型XSS是将XSS payload存储到服务端，所以又称为“持久性XSS”，存储型的XSS漏洞一般存在网站的后台，每次访问被攻击页面都会产生响应，

&#8195;&#8195;由存储型XSS的原理可知，一般存储型XSS都是由表单通过POST方法传输过去的，所以在进行存储型XSS的检测时，要对表单元素进行分析，分析过后才能通过构造Payload进行测试。

&#8195;&#8195;URL解析中利用正则表达式匹配出主域名，之后进行网址爬取的时候，就以这个主域名为限制，不属于主域名的网址删掉，在网页下载用HttpWEBResponse方法进行接收，接收下来的网页进行分析，解析漏洞可疑点分为两种，一种可疑点是在URL中，一种在HTML中，注入点可能存在URL中，也可能在Form表单中，所以要对这两个可疑点进行分析，将提取的信息和URL进行保存，以待Payload生成使用。

&#8195;&#8195;扫描前配置如图2-9所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片13.png"/></div>
<p align="center">图2-9  XSS扫描配置图</p>
&#8195;&#8195;扫描界面图如图2-10所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片14.png"/></div>
<p align="center">图2-10 XSS扫描界面</p>

&#8195;&#8195;扫描完成有提示如图2-11所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片15.png"/></div>
<p align="center">图2-11 XSS扫描完成提示图</p>

&#8195;&#8195;扫描结果如图2-12所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片16.png"/></div>
<p align="center">图2-12　XSS扫描完成图</p>

## 2.5.2XSS Payload生成

&#8195;&#8195;XSS Payload生成使用多线程，XSS Payload模块框架图如图2-13所示，在爬虫模块中提取的可疑漏洞点在这就用到了。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片17.png"/></div>
<p align="center">图2-13 XSS Payload生成模块总体设计图</p>

&#8195;&#8195;先根据可疑点分析注入点类型，注入点类型分为GET和POST，一般通过URL传参就是GET，表单一般都是用POST传参，在构造 Payload时，URL中的可疑漏洞的分析分为：第一种是直接在后面加攻击语句，第二种是通过正则表达式替换参数的值。表单要对表单数据进行分析，提取表单参数进行赋值，这些值都是攻击语句。攻击语句是收集的常用攻击语句以及它们的变种，包括：标签闭合、随机大小写、特殊字符嵌入、HTML实体编码、BASE64编码、Unicode编码、UTF编码、16进制编码等等。

## 2.5.3SQL注入Payload生成模块

&#8195;&#8195;SQL注入属于服务端应用安全，也是安全领域中最常见的攻击方式之一，如果说XSS是针对HTML的注入，那么SQL注入就是针对数据库的注入SQL注入的，Payload有相似之处，但攻击语句生成不同，SQL注入Payload可以分为有回显和无回显，但不管是基于时间的盲注还是基于布尔的盲注，Payload是在有回显的基础上生产的，所谓盲注就是，服务器关闭了错误回显，服务器没有回显，对于攻击者就缺少了非常重要的调试信息，所以盲注要比一般注入难度高，目前网络上现存的SQL注入大多为盲注，但是攻击者必须想办法知道注入是否成功，构造的语句是否有错误，所以就有了盲注技术。手工盲注过程一般是第一步判断是否存在注入，判断存在注入点后要判断注入类型，第二步是猜解当前数据表中的列数通，常用order by语句进行，第三步根据字段数猜解列名，第四步根据列名猜解数据，第五步查看是否能进行命令执行，写入Webshell进行提权操作，下一步是进行横向渗透或者纵向渗透。所以SQL注入Payload生成框架图如2-14所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片18.png"/></div>
<p align="center">图2-14SQL注入Payload生成框架图</p>

&#8195;&#8195;Sql注入流程如图2-15所示。

&#8195;&#8195;SQL注入按照数据提交方式进行分类可以分为：GET注入、POST注入、Cookie注入、HTTP头注入。在进行SQL注入检测时，数据的体提交方式中GET注入和POST注入比较容易理解，而Cookie注入又称为中转注入，在上文的XSS漏洞中已经提到Cookie，但未进行深究，为什么会有Cookie机制呢？在访问网页时，使用的协议是Http，而Http是无状态的，用户访问网站时，网站要对每一个会话进行认证，这样就很麻烦，所以就引入了Cookie和Session。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片19.png"/></div>
<p align="center">图4-15  SQL注入漏洞检测流程图</p>

&#8195;&#8195;就比如下课了，用户A要去吃饭，每一次去餐厅都是点餐都是独立的，用户A每次都要告诉服务员要吃什么和吃什么口味，但是比较会做生意的人，会记住用户的爱好和口味，会使用户体验良好，用户下次还来这个窗口吃饭。在用户访问网页时Http不会记录用户的状态，要想网站（服务员）记住用户是谁，就需要一个标注，这个标志就是Cookie，而Session是一种会话保持机制，就是用户在买饭时，刷过饭卡，商家给的一个就餐号，有了这个号，商家可以对用户直接服务，而不需要刷卡再进行排号。Cookie注入的注入点在Cookie中，主要测试手段就是更改Cookie里的字段值，进行测试。SQL注入漏洞测试图如4-10所示。

&#8195;&#8195;SQL注入检测和XSS漏洞检测有稍微的不同，因为在手工进行SQL注入时，要根据返回的提示信息进行测试，所以在每进行一步，就要进行回包分析，根据回包决定下一步发送的数据包，如果在基于时间的盲注中，数据包回复的时间不对，则考虑利用其他Payload，这个过程是个相互反馈，相互促进的过程，在漏洞检测中需要对Cookie的值进行分析，GET参数进行分析，对HTTP的头进行分析，对表单的值进行分析，分析后进行替换和发包。

# 2.6漏洞检测攻击语句加载

## 2.6.1系统默认

&#8195;&#8195;系统默认的Payload是写在系统。如图2-16所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片20.png"/></div>
<p align="center">图2-16 XSS攻击语句图</p>

&#8195;&#8195;在系统主页面由于本系统使用的漏洞扫描的方法是模式匹配，所以匹配语句如图2-17所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片21.png"/></div>
<p align="center">图2-17 XSS攻击匹配语句图</p>

## 2.6.2本地加载
&#8195;&#8195;在系统主页面可以对攻击语句进行加载。如图2-18所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片22.png"/></div>
<p align="center">图2-18 XSS攻击语句本地加载界面图</p>

&#8195;&#8195;点击字典加载即可添加如图2-19所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片23.png"/></div>
<p align="center">2-19 字典加载图</p>

# 2.7生成报告模块

## 2.7.1生成系统报告

&#8195;&#8195;扫描报告对于安全人员的收尾和撰写渗透测试报告很重要，在漏洞复现方面也很有用处在生成报告模块。首先是在系统上根据扫描出的漏洞进行分析，其次给出系统漏洞评分，最后生成报告。漏洞评分标准参考微软提出的DREAD模型，但是由于该系统只对两个漏洞进行分析，也不知道漏洞扫描出来的漏洞渗透进行的程度，所以该系统只对扫描漏洞数量进行分析。在报告中会记录扫描人员、扫描时间、扫描得分、给出修复建议以及Payload。报告保存分为三种方式，系统查看、本地保存、邮箱保存，系统查看是在系统上查看生成的报告，邮箱保存为将报告发送到指定邮箱，生成报告模块示意图如图2-20所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片23.png"/></div>
<p align="center">图2-20 生成报告流程图</p>

&#8195;&#8195;在报告生成模块，首先对扫描到的漏洞Payload进行记录，以便进行验证，再对扫到的漏洞做出修复建议，这些建议都是参考对应漏洞的常规修复方法，示例如图2-22所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片25.png"/></div>
<p align="center">图2-21 修复建议图</p>

&#8195;&#8195;在开始还会根据Payload进行记录，对漏洞情况做一个简单的分析，然后再根据数据生成图表，使得报告更加美观直接。在报告中会根据网站漏洞做出一个评分，报告如图2-23所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片26.png"/></div>
<p align="center">图2-23 报告图</p>

## 2.7.2本地文档报告

&#8195;&#8195;在进行本地报告保存中，导在做导出的报告时，首先要建立一个Word模板，在保存报告时，需要建立模板，要在模板中使用特殊占位符进行占位， 通过操作读写Word模板进行本地报告生成，其生成本地报告流程如2-24所示，当然本地模板只需要一次制作即可，不需要用户自行制作模板。在制作本地模板过程中需要注意的是占位符要特殊，可以使用多个特殊字符进行拼接。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片27.png"/></div>
<p align="center">图2-24 生成本地报告流程图</p>

&#8195;&#8195;在做导出的报告时，首先要建立一个Word模板，在建立模板时需要在模板中使用特殊占位符进行占位，在该系统的实现中，占位符为”[$]”，尽量避免使用的占位符在文档中使用，然后通过模块替换上系统产生的相应的数据，就可以生成一篇完整的漏洞测试报告了。报告模板如图2-25所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片28.png"/></div>
<p align="center">图2-25 导出报告模板图</p>

&#8195;&#8195;报告还可以保存到本地，在报告中有扫描人员姓名、扫描时间、扫描漏洞情况得分、扫描域名、还有在系统生产的图、修复建议、Payload、都能导入到文档中，导出文档主要用到的是Aspose插件进行的，在开发初期，使用的是另一个插件，但是由于它导出中文乱码的原因，所以就实现过程中换了一个插件。导出本地报告如图2-26所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片29.png"/></div>
<p align="center">图2-26 导出报告图</p>
## 2.7.3邮箱报告

&#8195;&#8195;邮件发送模块如图2-27所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片30.png"/></div>
<p align="center">图2-27 邮件发送模块界面图</p>

&#8195;&#8195;生成的报告还可以发送到指定邮箱，在该系统上登陆邮箱需要密码不是邮箱密码，而是在邮箱中生成的授权码，授权邮箱可以在第三方邮件系统上登陆，邮箱还要开通SMTP服务，如果服务没开通可能会导致发送邮件失败。
发送成功如图2-28所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片31.png"/></div>
<p align="center">图2-28 邮件发送模块界面图</p>

&#8195;&#8195;报告下载上半部分如图2-29所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片32.png"/></div>
<p align="center">图2-29 上半部分报告图</p>
&#8195;&#8195;报告下载下半部分如图2-30所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片33.png"/></div>
<p align="center">图2-30 下半部分报告图</p>

# 2.8帮助模块

## 2.8.1帮助模块

&#8195;&#8195;帮助界面如图2-31所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片34.png"/></div>
<p align="center">图2-31 使用帮助模块图</p>

&#8195;&#8195;排错流程如图2-32排错流程图所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片35.png"/></div>
<p align="center">图2-32 排错流程图</p>

&#8195;&#8195;此模块设计的意义在于如果在用户使用期间出现不可解决的问题或者有其他的需求可以联系开发人员进行反馈，以保证项目朝着贴合渗透的方向进行，加上这个模块不仅对系统使用人员有帮助，可以帮助使用人员进行排错，这对于项目的改进属于正反馈，引导项目的发展方向，当检测中断时可以进行排错。
可以打开使用帮助模块查看帮助信息，如图2-32所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片36.png"/></div>
<p align="center">图2-32 使用帮助模块图</p>
&#8195;&#8195;在主页面中还有一个模块是帮助模块，帮助模块包括两个内容，包括系统使用的文档帮助和视频帮助。文档帮助还是使用CHM文件，是系统的使用流程，而视频帮助是扫描的一个小例子。
&#8195;&#8195;视频帮助如图2-33所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片37.png"/></div>
<p align="center">图2-33 视频帮助模块图</p>

## 2.8.2漏洞知识库模块
&#8195;&#8195;漏洞知识库模块如图2-33所示。
<div align=center><img src="https://github.com/chain312/CScan/blob/master/WindowsFormsApplication1/image/图片38.png"/></div>
<p align="center">图2-33 漏洞知识库模块图</p>

&#8195;&#8195;在主页面中还有一个模块，为开发人员的介绍模块，在这个模块介绍了开发人员的基本信息，包括开发人员姓名、所属单位、电话、住址等信息。
