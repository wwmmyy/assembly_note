# 根据材料编程

## 材料

### 80x25彩色字符模式显示缓冲区(简称显示缓冲区)的结构

1. 内存地址空间中,B8000H~BFFFFH共32KB的空间,为80x25彩色字符模式的显示缓冲区.向这个地方写入数据,写入的内容将立刻出现在显示器上

2. 显示器可以显示25行,每行80个字符,每个字符可以有256种属性(背景色,前景色,闪烁,高亮等组合信息)

3. 一个字符在缓冲区占用两个字节,分别存放字符的ASCII码和属性

4. 80x25模式下,一屏的内容在缓冲区占用4000个字节  
显示缓冲区分为八页,每页4KB(约等于4000B),显示器可以显示任意一页的内容,默认第0页,也就是B8000~B8F9FH中的4000个字节的内容将显示在显示器上

5. 在一页缓冲区中:  
偏移000~09F对应显示器的第一行(80个字符占用160个字节)  
偏移0A0~13F对应第二行  
偏移140-1DF对应第三行  
第25行为F00~F9F

6. 每一行中,每两个字节代表一个字符  
00~01单元对应第一列  
02~03单元对应第二列

7. 每个字符的两个字节中,低位字节存放字符的ASCII码,高位字节存放字符的属性  
偶数地址放字符,奇数地址放属性

8. 字符属性的格式:  
7   6 5 4  3  2 1 0  
BL  R G B  I  R G B  
闪烁  背景 高亮  前景  
比如:红底高亮绿字,属性为01001010B  
红底闪烁绿字,属性为11000010B  
闪烁的效果必须在全屏dos下才能看到

## 编程任务

在屏幕中间分别显示绿色,绿底红色,白底蓝色的字符串"hello world"

## 代码

```assembly
```