# 数据处理的两个基本问题

1. 处理的数据在什么地方?
2. 要处理的数据有多长?

规定:用reg代表普通寄存器,sreg代表段寄存器,sreg为CS,SS,ES,DS

## 8.1 bx,si,di和bp

1. 在8086CPU中,只有这四个寄存器可以用在[...]中用来进行内存单元的寻址
2. 这四个寄存器可以单独出现,或者只能以四种组合出现
    1. bx和si
    2. bx和di
    3. bp和si
    4. bp和di
3. 只要在[...]中使用寄存器BP,而指令中没有显示给出段地址,那么段地址就默认在SS中,而非DS

## 8.2 机器指令处理的数据在什么地方

CPU内部,内存,端口

## 8.3 汇编语言中数据位置的表达

汇编语言中用三个概念来表达数据的位置

1. 立即数(idata)  
数据直接包含在机器指令中,比如mov ax,1
2. 寄存器  
指令要处理的数据在寄存器中,比如mov ax,bx
3. 段地址和偏移地址  
存放段地址的寄存器可以是默认的  
比如mov ax,[0],段寄存器默认在DS中  
mov ax,[bp],段寄存器默认在SS中  
也可以显示的给出,比如mov ax,es:[2]

## 8.4 寻址方式

定位内存单元的方式被称为寻址方式

## 8.5 指令要处理的数据有多长

8086CPU可以处理两种尺寸的数据,byte和word

mov ax,1    ->字操作  
mov al,1    ->字节操作  
用word/byte ptr操作符来显示指明操作单元的长度
mov word ptr ds:[0],1   ->字单元
mov byte ptr ds:[0],1   ->字节单元
pop和push指令只进行字操作

## 8.6 寻址方式的综合运用

略

## 8.7 div指令(除法)

div reg或者div 内存单元

1. 除数:8位或者16位,在一个reg或者内存单元中
2. 被除数:放在AX(除数8位)或者AX和DX中(除数16位),其中,AX放低位,DX放高位
3. 结果:如果是8位除数,AL存储商,AH储存余数;如果除数为16位,AX储存商,DX储存余数

## 8.8 伪指令dd

dd用来定义dword(double word,双字)型数据  
dd 1的含义:定义数据00000001H,占用四个字节

## 8.9 dup

配合db,dw,dd来使用,进行数据的重复

db 3 dup(0) ->定义三个字节,每个字节的值都是0  
db 2 dup(1,2)   ->等价于db 1,2,1,2
db 2 dup('ab','A')  ->等价于 db 'abAabA'

定义一个200字节的栈

```assembly
stack segment
    db 200 dup(0)   ;200字节,每个都是0
stack ends
```

## 实验七

```assembly
assume cs:code,ds:data

data segment
    db '1975','1976','1977','1978','1979','1980','1981','1982','1983','1984'
    db '1985','1986','1987','1988','1989','1990','1991','1992','1993','1994','1995'     ;表示年份的21个字符串

    dd 16,22,382,1356,2390,8000,16000,244486,50065,97479,140417,197514
    dd 345980,590827,803530,1183000,1843000,2759000,3753000,4649000,5937000                     ;21年总收入

    dw 3,7,9,13,28,38,130,220,476,778,1001,1442,2258,2793,4037,5635,8226
    dw 11542,14430,15257,17800                                                          ;21年雇员人数
data ends

table segment
    db 21 dup('year summ ne ?? ')
table ends

code segment
start:  mov ax,data
        mov ds,ax
        mov ax,table
        mov es,ax

        mov cx,21
        mov bx,0    ;年份偏移
        mov si,84   ;收入偏移
        mov di,168  ;人数偏移
        mov bp,0    ;结果首地址
        eachyear:   mov ax,[bx].0
                    mov es:[bp],ax
                    mov ax,[bx].2
                    mov es:[bp].2,ax    ;年份

                    mov byte ptr es:[bp].4,' '     ;空格

                    mov ax,[si].0
                    mov es:[bp].5,ax
                    mov ax,[si].2
                    mov es:[bp].7,ax    ;收入

                    mov byte ptr es:[bp].9,' '     ;空格

                    mov ax,[di].0
                    mov es:[bp].10,ax   ;人数

                    mov byte ptr es:[bp].12,' '    ;空格

                    ;计算人均收入
                    mov ax,[si].0
                    mov dx,[si].2
                    div word ptr [di]
                    mov es:[bp].13,ax   ;平均收入

                    mov byte ptr es:[bp].15,' '    ;空格

                    add bp,10h
                    add bx,4
                    add si,4
                    add di,2
        loop eachyear
        mov ax,4c00h
        int 21h
code ends

end start

```
