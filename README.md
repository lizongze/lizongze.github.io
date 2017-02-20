# 模拟面试：

我做过哪些记忆深刻的事：


### react组件自动化测试：（调研学习能力，虽不能装下所有知识，那至少要来一个hold住一个）
当时yo组件库写得差不多了，我被安排了调研react组件自动化测试。
由于我之前没接触过测试，我就先把当前存在的各种测试框架都大致了解了下：
+ google各种英文中文博客、github文档；
+ 向同行询问他们都是怎么测试的，
+ 在github搜索对应框架的带有测试的工程，看其测试环境和测试代码怎么编写的，是否有必要性；
+ 并学习了测试的一些基础理论，以及如何编写可测试的代码，当然还有哪些是可测的，是否有测试的必要性，等等；
+ 对各个框架的特点进行评估后，选了一个语法最优雅，性能最卓越，理念最先进的，新近出现的测试框架——ava.js；
+ 对于测UI还是测逻辑这个问题，觉得测UI太难了，就选择了只测逻辑，主要看模型的输入和输出的数据是否对应，并编写了RangeSlider组件的测试代码。 

最后，将搜集到的资料整合成了ppt，在组内进行了专题分享。自己也开始修炼如何编写可测试代码这门功课了。

后记：
+ 组内觉得测试所需时间过长，与开发时间可能1：1，就没有对所有组件写测试代码。
+ 到后面正式发版后，框架迭代的过程中，发现需要测试的。因为修改组件后，都只能手动点UI操作测试是否改出了bug，很不方便。
+ 后面vue的有关测试我也采用了ava。
+ ava成为了2016最流行的框架。


### fetch异步请求迭代过程（追求真理是一种执念）

我们用vue做一套后台数据管理系统，刚开始搭建环境，负责人让我选下主流的promise和fetch库，做下polyfill。（当时也是从没用过promise和fetch，没想到后面迭代了好多次）
+ 【第一版——init】最开始没想到这里面会碰到的问题，就选了下主流的库，引入了，可用就完了；

+ 【第二版——简单的错误处理】接着因为有的接口会抛出错误信息，要把这些信息提示出来，就引出了错误处理；

+ 【第三版——统一的错误处理中间件】然后，有的接口不太稳定，有网络错误，有的是提示前端逻辑错误，等等，有了不同的错误类型，于是就对不同的错误类型进行分类并统一做处理，统一封装成了中间件函数；

+ 【第四版——错误统一处理，异步action用async await重写】

  　　之前都是用的fetch、promise、then、catch的代码方式，在逻辑比较复杂（如鉴权），要调用多个接口，并且有依赖关系时，发现then()方式写的函数各种嵌套，代码一点也不优雅；

  　　并且在正常和错误情况都要关闭loading状态等类似逻辑的时候，代码开始变得冗余，要在catch后面再接个then来处理状态，很不直观；

  　　后来接触到了async await方法，配合 try catch finally，复杂逻辑下代码变得不能再更直观明了了。

+ 【第五版——逻辑与错误处理解耦分层，并支持主动、超时自动abort】

  　　后来听了某个项目的分享，参考其fetch处理时，发现将错误处理集中在一个函数内，是要清晰许多。而当前项目中每种错误类型在抛出时就被处理了，多个处理就显得很冗余，于是想将多个错误处理化成一个，类似多态那样。

  　　最后在核心函数里只管抛出错误并记录好错误类型，然后在外层函数中统一进行各种预处理，并将错误处理后再抛出一个错误（标记已处理）。
  　　这样核心请求函数变得异常清晰，错误处理集中在了一个函数内，代码优雅了太多。还顺手将那个项目用Promise.race实现的超时自动abort以及主动abort借鉴过来了。

到此，感觉这样的异步处理，已然趋近于真理了。


### 模糊匹配算法试炼（算法虐我千百遍，我待算法如初恋，直播走弯路）

做页面流转需求（从当前页面，通过某种action，进入下一个页面）时，有一个action字典，用户可以输入关键词来搜索有对应title的action。当时因为后端还没支持分页，每次都将全部5000多个序列扔到前端来，并且查询关键词，也还是扔到后端去，等待返回序列的。我就想既然全部数据都在前端了，干嘛不直接在前端本地做匹配呢，正好后端还没有支持模糊匹配。

（然而模糊匹配算法是个什么东西？好吃么？）
+ 首先，百度了下模糊匹配算法，发现有一个“最小编辑距离”算法。理解算法原理，发现是个动态规划，将两个字符串的其一进行“增”“删”“改”，使两字符串相同的操作（增删改）次数，用动态规划背包原理求出最小操作次数（也可加上对应权值算费用）。
+ 然后搜索js对应的库，没找到。于是跳到python里找现成模糊匹配库，果然python啥库都有，然后找到了库，并在readme中发现居然有js的翻版，然后又发现这模糊匹配算法居然引用了另一个号称最快的最小编辑距离算法（将二维数组变成一维了），于是将这段代码copy了过来，悲催地发现不支持中文匹配。
+ 接着为了支持中文，看了下算法源码，发现其用了转码来加快速度，转码没有支持中文，查了下将中文转成了unicode编码的函数escape，成功支持了中文。
+ 发现按最小编辑距离升序排列后，一些名字特别短的，老是能排在前面混淆视听，感觉是替换惹出的锅，于是修改了下算法，对不同操作赋予不同权值，按呈现序列的效果调了一下权值参数，并把只有很少的字相同的过滤掉了。

好了，这下终于perfect了。

后记：后来经提点，发现这是一个伪前端需求，因为以后action字典肯定会变得越来越大，是很有可能达到无法全部传递到前端的情况的，所以这是一个必须支持分页，并该直接扔关键词到后端，等后端返回序列的需求。果然没文化真可怕，见识少容易走弯路，耗费时间的事情要提前周知，看是否真有其必要。
