---
layout: post
title: 【随机模拟试验】枪手博弈问题（一）.
categories:
- Mathematics, the language of God
---


Author:Guofei

~本文是本人原创内容，如有逻辑、算法或其它错误，请联系[本人邮箱](mailto:guofei@foxmail.com)~


枪手博弈问题是博弈论中的一个著名的案例，然而大多数分析落脚在单轮博弈上。这里对多轮枪手博弈问题进行随机模拟试验。

# 问题引入：
首先介绍一下枪手博弈问题：

```
彼此痛恨的甲、乙、丙三个枪手准备决斗。甲枪法最好，十发八中；乙枪法次之，十发六中；丙枪法最差，十发四中。先提第一个问题：如果三人同时开枪，并且每人只发一枪；第枪战后，谁活下来的机会大一些？
```

一些人凭直观认为甲的枪法好，活下来的可能性大一些。但合乎推理的结论是，枪法最糟糕的丙活下来的几率最大。

解法是这样的：

```
枪手甲一定要对枪手乙先开枪。因为乙对甲的威胁要比丙对甲的威胁更大，甲应该首先干掉乙，这是甲的最佳策略。
同样的道理，枪手乙的最佳策略是第一枪瞄准甲。乙一旦将甲干掉，乙和丙进行对决，乙胜算的概率自然大很多。
枪手丙的最佳策略也是先对甲开枪。乙的枪法毕竟比甲差一些，丙先把甲干掉再与乙进行对决，丙的存活概率还是要高一些。
```

进一步思考，并且附加两个条件：

>1、按照甲、乙、丙、甲、乙、丙...的顺序轮流开枪，直到决斗场剩下最后一个人。
2、任何一人可以选择放空枪，从而不打中任何一个人。

附加这两个条件后，解答方法就没这么清晰了。

这里把这个问题叫做多轮枪手博弈问题，多轮枪手博弈问题仍然有一些好的性质：

>1、在决斗进行到第n轮后，如果甲、乙、丙都存活，那么这时的博弈问题与第1轮是同一个问题。也就是说策略与子策略是同构的。
2、尽管可能是无穷次博弈（大家一直都打不中的概率不为0），但是局中人的战略是有限的

程序实现：

此文不用推理方法，而是使用随机模拟方法。

把这三位枪手命名为`Leo`, `Raph`, `Mikey`.

不要在意忍者神龟为啥内讧了，毕竟这部动画里就一个妹子。

Leo的策略

```
function A=Leo(A,p1,p2,p3)
if A(1,1)==1&rand<p1
    if A(2,1)==1
        A(2,1)=0;
    elseif A(3,1)==1
        A(3,1)=0;
    else
    end
end
end
```

Leo和Raph的能动性很受限制,不多做解释了。

A代表3人的存活矩阵，这是一个3*1的矩阵，在这个矩阵中，1代表对应人暂时存活，0代表死亡。初始状态是这样：

>A=[1;
    1;
    1];

p1,p2,p3代表每人的命中率

>p1=0.8;
p2=0.6;
p3=0.4;

Raph和Leo一样，没有多少能动性。

```
function A=Raph(A,p1,p2,p3)
if A(2,1)==1&rand<p2
    if A(1,1)==1
        A(1,1)=0;
    elseif A(3,1)==1
        A(3,1)=0;
    else

    end
end
end
```

最弱的Mikey ，反而有很强的能动性
Mikey不放空枪的情况：

```
function A=Mikey(A,p1,p2,p3)
if A(3,1)==1&rand<p3
    if A(1,1)==1&A(2,1)==0
        A(1,1)=0;
    elseif A(1,1)==0&A(2,1)==1
        A(2,1)=0;
    elseif A(1,1)==1&A(2,1)==1
        A(1,1)=0;
    end
end
end
```

如果Leo和Raph都没死，那么Mikey故意放空枪：

```
function A=Mikey(A,p1,p2,p3)
if A(3,1)==1&rand<p3
    if A(1,1)==1&A(2,1)==0
        A(1,1)=0;
    elseif A(1,1)==0&A(2,1)==1
        A(2,1)=0;
    elseif A(1,1)==1&A(2,1)==1
        A=A;
    end
end
end
```

构建决斗场：

```
while sum(A)>=2%战斗到最后一个人为止
    A=Leo(A,p1,p2,p3);
    A=Raph(A,p1,p2,p3);
    A=Mikey(A,p1,p2,p3);
end
```

下面的程序把这场决斗重复一万次，并对结果进行计数

```
function C=main3()

B=[0;0;0];
for i=1:10000
    B=B+dual();
end
C=B./sum(B);
end
```

运行结果分析：

```
    Mikey放空枪的情况：
0.4669
0.0629
0.4702
```

以上分别是三只神龟的最终存活概率

>结论1：比起Mikey的渣枪法，其生还概率非常可观。真是”天下之至柔，驰骋天下之至坚“。

>结论2：实力第二的Raph最惨，生还的概率极其渺茫。二把手不好做。

>结论3：命中率0.8的Leo，命中率0.4的Mikey，生还概率居然相近。枪打出头鸟。

```
Mikey不放空枪的情况：
0.4490
0.0886
0.4624
```

>结论1：放不放空枪，对Leo, Raph的生存概率影响比较大，反而对自己影响很小。

>结论2：就算有老三帮助，二把手还是不好做

Mikey枪法和生还概率的关系图：（横坐标是命中率，纵坐标是生还概率）

![640](http://i.imgur.com/i7Utw8i.png)

```
结论：枪手博弈中的优势地位方，也是需要苦练内功的。
```

```
后记：
结论1：比起Mikey的渣枪法，其生还概率非常可观。真是”天下之至柔，驰骋天下之至坚“。
结论2：实力第二的Raph最惨，生还的概率极其渺茫。二把手不好做。
结论3：命中率0.8的Leo，命中率0.4的Mikey，生还概率居然相近。枪打出头鸟。
结论4：Mikey放不放空枪，对Leo, Raph的生存概率影响比较大，反而对自己影响很小。
结论5：就算有老三帮助，二把手还是不好做
结论6：枪手博弈中的优势地位方，也是需要苦练内功的。
```


*本文不足：本文在使用策略集时，用理论推导的方法剔除了大部分策略。虽然能够简化问题，但是缺点也比较大：一是不再是纯粹的随机模拟试验；二是在问题扩展时，代码的可移植性差。
希望在下一篇中能开发出泛用性更高的博弈论程序。*