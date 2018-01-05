# Spectre 攻击例程 #

2018年1月2日 (CVE-2017-5753 和 CVE-2017-5715)  "幽灵" Spectre 漏洞利用例子 


## 这是什么？ ##

我们把文本 "The Magic Words are Squeamish Ossifrage." 放在内存中, 然后我们试图利用漏洞读取他。如果系统易受到攻击, 那么你将在标准输出中看到相同的文本。


在本代码中, 如果 victim_function () 中的编译指令以严格的程序顺序执行, 则该函数只从 array1 [0.. 15] 中读取, 因为 array1 size = 16。但是, 执行时间超出读取时间是可能的。读取内存 byte() 函数对 victim_function () 进行多次训练调用, 以使分支预测器预期 x 的有效值, 然后调用界外的 x。条件分支无法预测, 随后的推测执行使用界外 x 读取一个谜之字节。然后, 预测代码从 array2 [array1 [x] * 512] 中读取, 将 array1 [x] 的值泄漏到缓存。为了完成攻击, 使用一个简单的刷新 + 探头来识别 array2 在哪个缓存行, 透露内存内容。攻击重复多次, 因此即使目标字节最初未被缓存, 第一次迭代也会将其带入缓存。在i7 surface Pro 3中，优化的代码读取速度大约 10 kb/秒 。


## 来源 ##

* [Spectre exploits info]
* [CVE-2017-5753] - Variant 1: bounds check bypass
* [CVE-2017-5715] - Variant 2: branch target injection

[Spectre paper]: <https://spectreattack.com/spectre.pdf>
[Spectre exploits info]: <https://spectreattack.com>
[CVE-2017-5753]: <http://www.cve.mitre.org/cgi-bin/cvename.cgi?name=2017-5753>
[CVE-2017-5715]: <http://www.cve.mitre.org/cgi-bin/cvename.cgi?name=2017-5715>
