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
2. 判断cx的值.为零,则向下执行,否则跳到标号处执行程序

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
