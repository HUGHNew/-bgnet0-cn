# 为什么会有这repo
这个repo是我对Beej大神的[Beej's Guide to Network Concepts](https://github.com/beejjorgensen/bgnet0)的翻译。我还记得我第一次看到Beej大神的文字是很多年前的一本基于c语言讲的计算机网络。按照Beej自己的[官网上](https://beej.us/guide/bgnet0/)说法，这一系列的文章算不上一本正式的书，是他大学上课的资料整理。不同于之前的c语言版本，这一资料用的是现在使用的最广泛的语言之一python来介绍原理，更加贴近整个学校的教学体系。

那么我为什么要翻译这一系列的内容呢，这也是我第一次“翻译”某种东西，感触良多（后面再来浅聊一下），对于我自己，触发内心行动的原因有:
* 工作了这么多年，越来越觉得自己变得喜欢评论甚于亲自动手去完成某事。有时候甚至不辨细节，先来高谈阔论一番，对错先不谈，而是急于站在话语的高点，心中虽然知道“绝知此事要躬行”，行动上都是“高谈阔论无重点“。而一个偶然的机会，看到Beej大神的这个教案，并且其中他提到欢迎翻译，当时心想，何不给自己一个机会，试试看自己还能不能真的“完成”一件事情。
* 如果问现在的我，人最需要的品质是什么，我会说是谦逊，韧性和保持好奇心。而“翻译”这个活动，我之前从未做过，这种没做过的事情，至少可以让我体会到这三点里面的两点，也是鞭策告诫自己的一种训练。
* beej大神的这些文字清晰，深入简出，加上内容并不是进阶型知识（但是并不代表他就写的很浅显，相反，逻辑很清晰），我读过一遍之后，即使我不是一个专业搞网络的，也觉得很容易理解，所以我想可能非常适合让我这种菜鸟翻译者试试。

# 翻译的体会
就像我上一节说的，虚长年岁，看的东西多了就以为自己能评论一切，特别是，这个时候很容易陷入“用其他经验来揣测根本没有涉足过的领域”。这次翻译让我体会良多。
* 很多时候发现自己写中文都快写不通顺了，所以，如果里面读起来不通顺的话，欢迎各位指正。
* 最开始高估了自己翻译的速度，因为这一系列文章最长的大概也就500行英文，但是真的翻译起来，这种500行的零零碎碎基本都要花一天（我基本都是晚上来做这件事），至少从我自己的角度看，翻译不是一件容易的事情。
* 校对，这又是一个我以前觉得“只要花时间”就行的事情，但是，实际上，这需要很多品质才能做的很优秀，细心，专注，客观，我自己是校对了一遍，但是质量，说实话，就和自己测试自己的程序一样，信心也就是个一般。

# 关于Beej大神文章本身
虽然这是一系列面向“入门者”的文章，但是其内容并不浅显，逻辑清晰，就算是我这种确实工作了很多年的，读一遍之后还是复习到了很多遗忘了很久的知识。确实，这些知识可能很难真的用上，但是，我永远觉得任何时候搞清楚一件事情而带来的愉悦是一种长期的快乐，它不会消失，甚至当你后面某天回忆起这个时刻的时候，还是愉悦的。

从大神的教案中，也可以看到，他的教学非常注重理论和实际结合，不是只有那种理论知识的堆叠，而是去旁枝，细说主枝的风格。我觉得这对现代传道方法非常重要，在搜索引擎，甚至AI如此发达的今天，细枝末节完全可以靠日后的搜索，调查来弥补，而主枝知识的理解，思维方式的培养，是需要一开始的累积。

大神的关于网络的这些教案不是按照国内常见的自底向上的方法，而是从应用层到物理层，从初学者的角度，从易于接触的到一般难以了解的。最后再介绍现实网络工作中的常用组件，比如防火墙，dig工具，数据安全，端口扫描，可以说并不是一个“虚空亭台”式的教案。

这里我要插一句，这一系列教案里面关于物理层是我读过的入门书籍里面最详细的，我真的是从这里大概知道了各个线到底有什么差别。

这些教案每隔几个就有几个要动手的项目，并提供框架代码和非常详细的评分表（这一点属于让我印象深刻的部分之一），可以让你知道你学给自己的到底是一个什么水平。

总之，非常推荐，特别是，如果是因为我的翻译水平有限而导致对这一系列教案产生不该有的负面评价，我应该会感到很愧疚。

# 欢迎改错
因为我是第一次翻译+自己校对，难免错误很多，如若发现，不吝赐教。
另外，为了最大程度减少文档中比如链接的问题，我保留原repo的所有结构，只是把其中的markdown的内容翻译成了中文。所有的markdown 都位于src文件夹内。
