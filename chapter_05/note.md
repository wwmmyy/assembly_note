# [bx]和loop指令

## [bx]和内存单元的描述

在

```assembly
mov ax,[0]
```

指令中,表示将一个内存单元的内容送入ax,这个单元的长度为2个字节(字单元),存放一个字,偏移地址为0,段地址放在DS寄存器

在

```assembly
mov al,[0]
```

指令中,表示将一个内存单元的内容送入al,这个内存单元的长度为一个字节(字节单元),存放一个字节,偏移地址为0,段地址在DS中

[bx]同样表示一个内存单元,在

```assembly
mov ax,[bx]
```

指令中,将一个长度为2的内存单元的内容送入ax,段地址在DS中,偏移地址在bx中

## loop

loop表示循环

## 定义的描述性符号"()"

为了方便,今后使用()表示一个寄存器或者一个内存单元中的内容

比如(ax)表示AX中的内容,(al)表示AL中的内容,(20000H)表示内存单元20000H的内容

((ds)*16+(bx))表示为  
DS内容*16+BX内容结果的值的内存单元的内容

## 约定符号idata表示常量

比如mov ax,[idata]代表mov ax,[1]|mov ax,[2]|mov ax,[4]|...  
mov bx,idata则表示mov bx,1|mov bx,2|mov bx,10|...

## 5.1 [BX]

mov ax,[bx]  
解释为:(ax)=((ds)*16+(bx))

mov [bx],ax  
解释为:((ds)*16+(bx))=(ax)

## 5.2 loop指令

loop指令格式:  
loop 标号

CPU执行loop指令时,进行两步操作

1. (cx)=(cx)-1;将CX内容减一
2. 判断cx的值.为零,则向下执行,否则跳到标号处执行

### Example:计算2^12

```assembly
assume cs:code

code segment

    mov ax,2
    mov cx,11

    s:  add ax,ax
    loop s

    mov ax,4c00h
    int 21h

code ends

end
```

总结:cx中存放循环次数,loop和标号之间存放循环体

## 5.3 在Debug中跟踪用loop指令实现的循环程序

Q:计算ffff:0006单元中的数乘以3,结果储存在dx  
A:  
一个内存单元中的数乘以三不会大于两个字节,不会超过dx的存储范围  
使用loop来将目标单元中的内容add两次  
由于目标内存单元为一个字节,寄存器为两个字节,所以我们将目标内存的值储存在al中,将ah置零,但是使用ax进行add操作,因为(ax)在这个时候等于(al)  
以下是汇编代码

```assembly
assume cs:code
code segment
    mov ax,0ffffh
    mov ds,ax
    mov bx,6
    mov al,[bx]
    mov ah,0
    mov dx,0

    mov cx,3
    s:  add dx,ax

    mov ax,4c00h
    int 21h
code ends
end
```

使用p命令执行loop可以一次性执行完这个loop  
使用g命令+偏移地址的方式可以将程序执行到指定位置处

## 5.4 Debug和汇编编译器masm对指令的不同处理

在Debug中  
mov ax,[0]  
表示将ds:0处的数据送入ax  
在汇编源程序中,以上命令被当作"mov ax,0"

如何解决这个差异造成的不一致呢,使用bx和[bx]

## 5.5 loop和[bx]联合应用

Q:计算ffff:0000~ffff:b单元中数据的和  
A:分析  
这bH个单元中的和不会超过一个寄存器的最大范围,可以用寄存器来储存结果,放在dx中  
由于单个字节相加会导致溢出,因此不能使用al这类八位寄存器  
用16位寄存器储存八位的单个字节,然后将16位寄存器的值相加  
程序如下

```assembly
assume cs:code
code segment

    mov ax,0ffffh
    mov ds,ax

    mov dx,0
    mov ah,0
    mov bx,0
    mov cx,0ch
    s:  mov al,[bx]
        inc bx
        add dx,ax
    loop s

    mov ax,4c00H
    int 21H

code ends
end
```

## 段前缀

在"mov ax,[0]"中,段地址默认在ds中,也可以显式指出段地址所在的段寄存器,比如  
mov ax,es:[2]  
mov ax,ss:[bx]  
mov ax,cs:[bx]  
mov ax,ss:[0]

## 5.7 一段安全的空间

内存中可能存放着重要的代码(不属于自身的代码),随意修改任意内存可能导致错误

安全的空间:0:200~0:2ff,这256个字节的空间认为是安全的

> 实验四的第三题,为什么要有多余的mov cx,cx呢?
