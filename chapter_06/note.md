# 包含多个段的程序

## 6.1 在代码段中使用数据

使用end+标号来设置程序的入口地址,eg:  
start:  xxxx  
end start

## 6.2 在代码中使用栈

```assembly
assume cs:code
code segment

dw 0123h,0456h,0789h,0abch,0defh,0fedh,0cbah,0987h  ;数据
dw 0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0  ;被当作栈来使用

start:  mov ax,cs           ;由于不能直接赋值使用ax做中转
        mov ss,ax           ;指明栈段
        mov sp,30h          ;设置栈顶

        mov bx,0
        mov cx,8
        s:  push cs:[bx]    ;将数据依次入栈
            add bx,2
        loop s

        mov bx,0
        mov cx,8
        t:  pop cs:[bx]     ;依次出栈
            add bx,2
        loop t

mov ax,4c00h
int 21h

code ends
end start   ;指明程序入口
```

## 6.3 将数据,代码,栈放入不同的段

```assembly
assume cs:code,ds:data,ss:stack
;数据段
data segment
    dw 0123h,0456h,0789h,0abch,0defh,0fedh,0cbah,0987h
data ends
;栈段
stack segment
    dw 0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0
stack ends
;代码段
code segment
start:  mov ax,stack
        mov ss,ax       ;设置寄存器SS,指定栈段
        mov sp,20h      ;设置栈顶
        mov ax,data
        mov ds,ax       ;设置数据段

        mov bx,0
        mov cx,8
        s:  push [bx]
            add bx,2
        loop s

        mov bx,0
        mov cx,8
        t:  pop [bx]
            add bx,2
        loop t

    mov ax,4c00h
    int 21h
code ends
end start               ;程序从start标号处开始
```

mov ax,stack中,stack被替换为一个实际的地址,在8086CPU中,段寄存器不可以用mov直接赋值,需要ax这类寄存器来中转赋值
