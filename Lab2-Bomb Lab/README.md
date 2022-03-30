# Bomb!

***

> Dismantling binary bombs.

### Hints

***

* Windows 分屏技巧：win + 方向键
  
* If you run your bomb with a command line argument, for example,
  
  ```
  linux> ./bomb psol.txt
  ```
  
  then it will read the input lines from `psol.txt` until it reaches `EOF` (end of file), and then switch over to `stdin`. In a moment of weakness, Dr. Evil added this feature so you don’t have to keep retyping the solutions to phases you have already defused.
  
* Methods in official tutorial of `debugger`:
  * how to **single-step** through the assembly code
  * how to set **breakpoints**
  * how to inspect both the **registers** and the **memory** states

* tips：
  * `gdb`(GNU debugger)
  * `objdump -t`
    * This will print out the **bomb's symbol table**. The symbol table includes **the names of all functions** and **global variables** in the bomb, the names of all the functions the bomb calls, and their addresses. You
      may learn something by looking at the function names!
  * `objdump -d`
    * Use this to disassemble all of the code in the bomb.
  * `commands `
    * `apropos`, `man`, and `info` are your friends.
    * `man ascii`.
    * `info gas` will give you more than you ever wanted to know about the GNU Assembler.

### Basic Knowledge

***

* `handin`：提交

* `gdb`：

  > 汇总

  ```
  （gdb）help：查看命令帮助，具体命令查询在gdb中输入help + 命令,简写h
  
  （gdb）run：重新开始运行文件(run-text：加载文本文件，run-bin：加载二进制文件),简写r
  
  （gdb）start：单步执行，运行程序，停在第一执行语句
  
  （gdb）list：查看原代码(list-n,从第n行开始查看代码;list+ 函数名:查看具体函数),简写l
  
  （gdb）set：设置变量的值
  
  （gdb）next：单步调试（逐过程，函数直接执行）,简写n
  
  （gdb）step：单步调试（逐语句：跳入自定义函数内部执行）,简写s
  
  （gdb）backtrace：查看函数的调用的栈帧和层级关系,简写bt
  
  （gdb）frame：切换函数的栈帧,简写f
  
  （gdb）info：查看函数内部局部变量的数值,简写i
  
  （gdb）finish：结束当前函数，返回到函数调用点
  
  （gdb）continue：继续运行,简写c
  
  （gdb）print：打印值及地址,简写p
  
  （gdb）quit：退出gdb,简写q
  
  （gdb）break+num：在第num行设置断点,简写b
  	b 函数名
  	b 行号
  	b 文件名:行号
  	b 行号 if条件
  	
  （gdb）info breakpoints：查看当前设置的所有断点
  
  （gdb）delete breakpoints num：删除第num个断点,简写d
  
  （gdb）display：追踪查看具体变量值
  
  （gdb）undisplay：取消追踪观察变量
  
  （gdb）watch：被设置观察点的变量发生修改时，打印显示
  
  （gdb）i watch：显示观察点
  
  （gdb）enable breakpoints：启用断点
  
  （gdb）disable breakpoints：禁用断点
  
  （gdb）x：查看内存x/20xw 显示20个单元，16进制，4字节每单元
  
  （gdb）run argv[1] argv[2]：调试时命令行传参
  
  core文件：先用$ ulimit -c 1024 开启core，当程序出错会自动生成core文件。调试时 gdb a.out core
  
  ctrl+c：退出输入
  ```

  > 分说

  * 显示汇编代码：disassemble 或简写为 `disas`

    ```C++
    　　//结合不同参数达到不同的显示效果，几个经典的用法
    　　disas /m func1 //显示函数func1的汇编
    　　disas $pc,+10 //显示pc位置开始的10字节汇编内容
    ```

  * 显示内存值

    ```C++
    x 内存地址   //查看该地址指向的内容(默认16进制)
    x /nfu buf
    /*n表示要显示的内存单元的个数
      f表示显示方式,可取如下值,比如x-安16进制,d-按十进制
      u表示一个地址单元的长度,b-字节,h-双字,w-4字,g-8字*/
    x/s 首位置   //可打印出对应字符串
    x/c 位置     //打印此位置的字符
    ```
    
    补充1
    
    ```
    (gdb) `x $pc`：显示程序指针指向位置的内容。
    (gdb) `x/i $pc`：显示程序当前位置的汇编指令。
    (gdb) `x/10i $pc`：显示程序当前位置开始往后的10条汇编指令。
    (gdb) `disassem $pc`：反汇编当前函数。简写为：`disas $pc`。
    ```
    
  * 打印

    ```C++
    p 位置 //打印值以及
    ```

  * 断点

    ```assembly
    info breakpoints ;查看所有断点
    break sum ;Set breakpoint at the entry to function sum
    break *0x80483c3 ;Set breakpoint at address 0x80483c3
    delete 1 ;Delete breakpoint 1
    disable 1 ;Disable the breakpoint 1
              ;(gdb numbers each breakpoint you create)
    enable 1 ;Enable breakpoint 1
    delete ;Delete all breakpoints
    clear sum ;Clear any breakpoints at the entry to function sum   
    ```

    断点有编号`id`,可以通过`info breakpoints`查看

* `core dump`

  在程序崩溃时，一般会生成一个文件叫core文件。core文件记录的是程序崩溃时的内存映像，并加入调试信息。core文件生成的过程叫做core dump。







### Phase_1

***

* 反汇编出`phase_1`

  ```assembly
  Dump of assembler code for function phase_1:                           0x0000000000400ee0 <+0>:     sub    $0x8,%rsp                        
  0x0000000000400ee4 <+4>:     mov    $0x402400,%esi                   
  0x0000000000400ee9 <+9>:     callq  0x401338 <strings_not_equal>     
  0x0000000000400eee <+14>:    test   %eax,%eax                        
  0x0000000000400ef0 <+16>:    je     0x400ef7 <phase_1+23>            
  0x0000000000400ef2 <+18>:    callq  0x40143a <explode_bomb>          
  0x0000000000400ef7 <+23>:    add    $0x8,%rsp                        
  0x0000000000400efb <+27>:    retq                                 
  End of assembler dump.
  ```

  测试输入字符串的首地址(char *)在哪个参数

  ```C++
  //instruction
  (gdb) x 0x403400
  //answer
  0x402400:       "Border relations with Canada have never been better." 
  ```

  在`strings_not_equal`函数入口设置断点

  ```C++
  //instruction
  break strings_not_equal
  //answer
  Breakpoint 1 at 0x401338
  ```

  运行输入字符串"hhhfff",打印`%rdi`的值

  ```
  //instruction(注意默认情况下是十进制)
  print $rdi
  //answer
  $1 = 6305664 
  //hex format
  print /x $rdi or info registers查看此时所有寄存器的值
  //anser
  $2 = 0x603780
  ```

  由此可见输入后进入`strings_not_equal`时，输入的字符串的首地址存在了`%rdi`中

### Phase_2

***

* 参数顺序(register)

  > %rdi	%rsi	%rdx	%rcx	%r8	%r9

* 先确定栈分析的习惯

  <img src="D:\github\CSAPP\images\lab2_phase_2_1.png" alt="lab2_phase_2_1" style="zoom:30%;" />

* 分析

  执行指令`(gdb) disas phase_2`,得到汇编代码

  ```assembly
  Dump of assembler code for function phase_2:        
  0x0000000000400efc <+0>:     push   %rbp            
  0x0000000000400efd <+1>:     push   %rbx        
  0x0000000000400efe <+2>:     sub    $0x28,%rsp                   
  0x0000000000400f02 <+6>:     mov    %rsp,%rsi                 
  0x0000000000400f05 <+9>:     callq  0x40145c <read_six_numbers>             
  0x0000000000400f0a <+14>:    cmpl   $0x1,(%rsp)                  
  0x0000000000400f0e <+18>:    je     0x400f30 <phase_2+52>        
  0x0000000000400f10 <+20>:    callq  0x40143a <explode_bomb>      
  0x0000000000400f15 <+25>:    jmp    0x400f30 <phase_2+52>        
  0x0000000000400f17 <+27>:    mov    -0x4(%rbx),%eax              
  0x0000000000400f1a <+30>:    add    %eax,%eax                    
  0x0000000000400f1c <+32>:    cmp    %eax,(%rbx)                  
  0x0000000000400f1e <+34>:    je     0x400f25 <phase_2+41>        
  0x0000000000400f20 <+36>:    callq  0x40143a <explode_bomb>      
  0x0000000000400f25 <+41>:    add    $0x4,%rbx                    
  0x0000000000400f29 <+45>:    cmp    %rbp,%rbx                    
  0x0000000000400f2c <+48>:    jne    0x400f17 <phase_2+27>        
  0x0000000000400f2e <+50>:    jmp    0x400f3c <phase_2+64>        
  0x0000000000400f30 <+52>:    lea    0x4(%rsp),%rbx               
  0x0000000000400f35 <+57>:    lea    0x18(%rsp),%rbp              
  0x0000000000400f3a <+62>:    jmp    0x400f17 <phase_2+27>        
  0x0000000000400f3c <+64>:    add    $0x28,%rsp                   
  0x0000000000400f40 <+68>:    pop    %rbx                         
  0x0000000000400f41 <+69>:    pop    %rbp                         
  0x0000000000400f42 <+70>:    retq                             
  End of assembler dump.  
  ```

  观察到调用了函数`<read_six_numbers>`,于是反汇编该函数：

  `(gdb) disas read_six_numbers`

  ```assembly
  Dump of assembler code for function read_six_numbers:               0x000000000040145c <+0>:     sub    $0x18,%rsp                   
  0x0000000000401460 <+4>:     mov    %rsi,%rdx                    
  0x0000000000401463 <+7>:     lea    0x4(%rsi),%rcx               
  0x0000000000401467 <+11>:    lea    0x14(%rsi),%rax              
  0x000000000040146b <+15>:    mov    %rax,0x8(%rsp)               
  0x0000000000401470 <+20>:    lea    0x10(%rsi),%rax              
  0x0000000000401474 <+24>:    mov    %rax,(%rsp)                  
  0x0000000000401478 <+28>:    lea    0xc(%rsi),%r9                
  0x000000000040147c <+32>:    lea    0x8(%rsi),%r8                
  0x0000000000401480 <+36>:    mov    $0x4025c3,%esi               
  0x0000000000401485 <+41>:    mov    $0x0,%eax                    
  0x000000000040148a <+46>:    callq  0x400bf0 <__isoc99_sscanf@plt>          
  0x000000000040148f <+51>:    cmp    $0x5,%eax                    
  0x0000000000401492 <+54>:    jg     0x401499 <read_six_numbers+61>          
  0x0000000000401494 <+56>:    callq  0x40143a <explode_bomb>      
  0x0000000000401499 <+61>:    add    $0x18,%rsp                   
  0x000000000040149d <+65>:    retq                             
  End of assembler dump.
  ```

  确定`<__isoc99_sscanf@plt>`参数数量，根据

  > 0x000000000040148f <+51>:    cmp    $0x5,%eax

  得知是6(>5)个参数(此处也是处理参数不够时的异常处理)

  注意到第一个存放的参数`%rsi`, 值为一个地址

  ```assembly
  0x0000000000401480 <+36>:    mov    $0x4025c3,%esi 
  ```

  注意第一个参数是一个地址值(指针),分析`sscanf`函数的参数发现该参数为`const char* format`输入的内容会存入根据buffer以及格式化字符串写入到变量中

  ```C
  //definition:方向为左buffer -write(by format)-> ...
  int sscanf(const char *str, const char *format, ...);
  //Example
  int day, year;char weekday[20], month[20], dtm[100];
  strcpy( dtm, "Saturday March 25 1989" );
  sscanf( dtm, "%s %s %d  %d", weekday, month, &day, &year );//流方向-->
  ```
  
  对`<read_six_numbers>`进行分析，在函数体重看到`<__isoc99_sscanf@plt>`因此可以根据
  
  ```assembly
  mov    $0x4025c3,%esi 
  ```
  
  执行指令`(gdb) x/s 0x4025c3`查看第一个参数格式
  
  > 0x4025c3:       "%d %d %d %d %d %d"
  
  注意到该函数入口处的`%esi`被用来传`string format`地址，因此前四个参数分别用寄存器`rdx,rcx,r8,r9`来传递，最后两个参数用栈传递，寄存器和栈空间传递的都是地址值，register之间(如`mov    %rsi,%rdx`)用的`mov`指令，因为`rsi`本身值就是地址值，register和memory或memory之间(通过`rax`做中介)用的指令为`lea`
  
  <img src="D:\github\CSAPP\images\lab2_phase_2_2.png" alt="lab2_phase_2_2" style="zoom:30%;" />
  
  回到`phase_2`
  
  ```assembly
  Dump of assembler code for function phase_2:        
  0x0000000000400efc <+0>:     push   %rbp            
  0x0000000000400efd <+1>:     push   %rbx        
  0x0000000000400efe <+2>:     sub    $0x28,%rsp                   
  0x0000000000400f02 <+6>:     mov    %rsp,%rsi                 
  0x0000000000400f05 <+9>:     callq  0x40145c <read_six_numbers>             
  0x0000000000400f0a <+14>:    cmpl   $0x1,(%rsp)                  
  0x0000000000400f0e <+18>:    je     0x400f30 <phase_2+52>        
  0x0000000000400f10 <+20>:    callq  0x40143a <explode_bomb>      
  0x0000000000400f15 <+25>:    jmp    0x400f30 <phase_2+52>        
  0x0000000000400f17 <+27>:    mov    -0x4(%rbx),%eax              
  0x0000000000400f1a <+30>:    add    %eax,%eax                    
  0x0000000000400f1c <+32>:    cmp    %eax,(%rbx)                  
  0x0000000000400f1e <+34>:    je     0x400f25 <phase_2+41>        
  0x0000000000400f20 <+36>:    callq  0x40143a <explode_bomb>      
  0x0000000000400f25 <+41>:    add    $0x4,%rbx                    
  0x0000000000400f29 <+45>:    cmp    %rbp,%rbx                    
  0x0000000000400f2c <+48>:    jne    0x400f17 <phase_2+27>        
  0x0000000000400f2e <+50>:    jmp    0x400f3c <phase_2+64>        
  0x0000000000400f30 <+52>:    lea    0x4(%rsp),%rbx               
  0x0000000000400f35 <+57>:    lea    0x18(%rsp),%rbp              
  0x0000000000400f3a <+62>:    jmp    0x400f17 <phase_2+27>        
  0x0000000000400f3c <+64>:    add    $0x28,%rsp                   
  0x0000000000400f40 <+68>:    pop    %rbx                         
  0x0000000000400f41 <+69>:    pop    %rbp                         
  0x0000000000400f42 <+70>:    retq                             
  End of assembler dump.  
  ```
  
  关键点是<+14>处的`cmpl $0x1,(%rsp)`比较的只是低32位，以及<+30>处的双倍处理和<+48>的循环标志。

<img src="D:\github\CSAPP\images\lab2_phase_2_3.png" alt="lab2_phase_2_3" style="zoom:25%;" />



### Phase_3

***

* 跳转(AT&T格式)

  ```assembly
  ;direct jump
  jmp lable
  
  ;jump to the address whose value is stored in %rax.
  jmp *%rax
  
  ;jump to the address the value(a address too) located in the address described by %rax points to.
  jmp *(%rax)
  
  ;jump to the address the value(a address too) located in this memory points to.
  jmp *memory address 
  
  examples:
  jmpq *0x402470(,%rax,8) ;见下方分析
  ```

  或者这么理解，假设指针p是一个地址，解引用后其值*p也是一个地址

  那么

  ```C++
  jmp *%rax -> jmp p
  jmp *(%rax) -> jmp *p
  ```

* 分析

  反汇编`phase_3`函数

  ```assembly
  Dump of assembler code for function phase_3:                       0x0000000000400f43 <+0>:     sub    $0x18,%rsp                   
  0x0000000000400f47 <+4>:     lea    0xc(%rsp),%rcx               
  0x0000000000400f4c <+9>:     lea    0x8(%rsp),%rdx               
  0x0000000000400f51 <+14>:    mov    $0x4025cf,%esi               
  0x0000000000400f56 <+19>:    mov    $0x0,%eax                    
  0x0000000000400f5b <+24>:    callq  0x400bf0 <__isoc99_sscanf@plt>          
  0x0000000000400f60 <+29>:    cmp    $0x1,%eax   ;确定sscanf参数数量为2    
  0x0000000000400f63 <+32>:    jg     0x400f6a <phase_3+39>        
  0x0000000000400f65 <+34>:    callq  0x40143a <explode_bomb>      
  0x0000000000400f6a <+39>:    cmpl   $0x7,0x8(%rsp)               
  0x0000000000400f6f <+44>:    ja     0x400fad <phase_3+106>       
  0x0000000000400f71 <+46>:    mov    0x8(%rsp),%eax               
  0x0000000000400f75 <+50>:    jmpq   *0x402470(,%rax,8)           
  0x0000000000400f7c <+57>:    mov    $0xcf,%eax                   
  0x0000000000400f81 <+62>:    jmp    0x400fbe <phase_3+123>       
  0x0000000000400f83 <+64>:    mov    $0x2c3,%eax                  
  0x0000000000400f88 <+69>:    jmp    0x400fbe <phase_3+123>       
  0x0000000000400f8a <+71>:    mov    $0x100,%eax                  
  0x0000000000400f8f <+76>:    jmp    0x400fbe <phase_3+123>       
  0x0000000000400f91 <+78>:    mov    $0x185,%eax                  
  0x0000000000400f96 <+83>:    jmp    0x400fbe <phase_3+123>       
  0x0000000000400f98 <+85>:    mov    $0xce,%eax                   
  0x0000000000400f9d <+90>:    jmp    0x400fbe <phase_3+123>       
  0x0000000000400f9f <+92>:    mov    $0x2aa,%eax                  
  0x0000000000400fa4 <+97>:    jmp    0x400fbe <phase_3+123>       
  0x0000000000400fa6 <+99>:    mov    $0x147,%eax                  
  0x0000000000400fab <+104>:   jmp    0x400fbe <phase_3+123>       
  0x0000000000400fad <+106>:   callq  0x40143a <explode_bomb>      
  0x0000000000400fb2 <+111>:   mov    $0x0,%eax                    
  0x0000000000400fb7 <+116>:   jmp    0x400fbe <phase_3+123>       
  0x0000000000400fb9 <+118>:   mov    $0x137,%eax                  
  0x0000000000400fbe <+123>:   cmp    0xc(%rsp),%eax               
  0x0000000000400fc2 <+127>:   je     0x400fc9 <phase_3+134>       
  0x0000000000400fc4 <+129>:   callq  0x40143a <explode_bomb>      
  0x0000000000400fc9 <+134>:   add    $0x18,%rsp                   
  0x0000000000400fcd <+138>:   retq                             
  End of assembler dump.
  ```

  同理，根据

  > 0x0000000000400f60 <+29>:    cmp    $0x1,%eax
  >
  > 0x0000000000400f63 <+32>:    jg     0x400f6a <phase_3+39>  

  确定`sscanf`的输入入口参数数量为2(注意下面是`jg`，所以是1+1；如果是`jne`或`jge`输入入口参数就是1)

  对中间段的等价处理

  ```C++
  // p1 in rdx; p2 in rcx; p_num in eax
  //eax==2
  if(p1>7)
      goto: explode_bomb();
  else{
      p1->eax;//注意movl 0x8(%rsp),%eax会把高32-bit清零
      goto: address(8*eax+0x402470);//0x402470+8*p1
      $0xcf(210)->eax;//还会是别的值
      goto: address(phase_3 + 123);
  }
  <phase_3+123>:
  if(eax==p2)
      defuse();
  else
      bomb();
  ```

  跳转句

  > jmpq   *0x402470(,%rax,8)

  跳转的目标地址的是内存(0x402470+%rax*8)处的值(也是一个地址)，假设%rax==0使用`x 0x403470`:

  > (gdb) x 0x402470                                                 0x402470:       0x00400f7c

  得到地址`0x00400f7c`即可找到相应位置执行代码，由此可见答案是不唯一的，最简单的做法就是令p1=0,p2=0xcf(207)



### Phase_4

***

* `test`指令

  * `TEST AX,BX` 与 `AND AX,BX` 命令效果相同，只是Test指令不改变AX和BX的内容，而AND指令会把结果保存到AX中。

    ```assembly
     test %eax %eax;判断%eax为负数,整数还是0
     je ...;jz同理,当eax==0时才会跳转
    ```

* `sar %eax`生疏指令

  * 该指令只有一个操作数，因此其意义就是算数右移`1`位!!!

* 反汇编得到函数的汇编代码

  ```assembly
  Dump of assembler code for function phase_4:                   0x000000000040100c <+0>:     sub    $0x18,%rsp                   
  0x0000000000401010 <+4>:     lea    0xc(%rsp),%rcx               
  0x0000000000401015 <+9>:     lea    0x8(%rsp),%rdx               
  0x000000000040101a <+14>:    mov    $0x4025cf,%esi               
  0x000000000040101f <+19>:    mov    $0x0,%eax                    
  0x0000000000401024 <+24>:    callq  0x400bf0 <__isoc99_sscanf@plt>           
  0x0000000000401029 <+29>:    cmp    $0x2,%eax                    
  0x000000000040102c <+32>:    jne    0x401035 <phase_4+41>        
  0x000000000040102e <+34>:    cmpl   $0xe,0x8(%rsp)               
  0x0000000000401033 <+39>:    jbe    0x40103a <phase_4+46>        
  0x0000000000401035 <+41>:    callq  0x40143a <explode_bomb>      
  0x000000000040103a <+46>:    mov    $0xe,%edx                    
  0x000000000040103f <+51>:    mov    $0x0,%esi                    
  0x0000000000401044 <+56>:    mov    0x8(%rsp),%edi               
  0x0000000000401048 <+60>:    callq  0x400fce <func4>             
  0x000000000040104d <+65>:    test   %eax,%eax                    
  0x000000000040104f <+67>:    jne    0x401058 <phase_4+76>        
  0x0000000000401051 <+69>:    cmpl   $0x0,0xc(%rsp)               
  0x0000000000401056 <+74>:    je     0x40105d <phase_4+81>        
  0x0000000000401058 <+76>:    callq  0x40143a <explode_bomb>      
  0x000000000040105d <+81>:    add    $0x18,%rsp                   
  0x0000000000401061 <+85>:    retq                             
  End of assembler dump.
  ```

  处理等价代码,注意参数数量为2

  ```C++
  // p1 in rdx, p2 in rcx
  //从<+34>开始
  if(p1<=14)
  {
      14->edx;
      0->esi;
      p1->edi;
      func4(edi,esi,edx);//<==>func4(p1,0,14)
      // test %eax %eax;判断%eax是否为0
      if(eax!=0)
          bomb();
      if(p2==0)
          return;
      else 
          bomb();
  }
  else
      bomb();
  //由eax==0推出p1的值,且p2==0
  
  <func4>:
  //p1 in edi;p2 in esi;p3 in edx;v in ecx;res in eax
  //initial value:p1=p1;p2=0;p3=14
  int res,v;//eax,ecx
  int func4(int &p1,int &p2,int &p3)
  {
      res=p3-p2;
      v=res>>31;//key step:0
      res+=v;//ignore
      res/=2;//res==7
      v=res+p2;//7
      if(p1<v)
      {
          p3=v-1;
          func4(p1,p2,p3);//参数是引用类型
          res*=2;
          return res;
      }
      else
      {
          res=0;
          //if(v>=p1) return res;//其实相当于==
          if(p1==v) return res;
          //p1>v时的操作
          p2=v+1;
          func4(p1,p2,p3);
          res=2*res+1;
          return res;
      }
  }
  //最终res==0
  1.全程p1未被修改
  2.最内层状态 v==p1 res=0;即v==p1,res=0
  3.回溯于第一段可以一直保持res==0:res*2=0
  4.只要p1==7即可,p1==3,p1==1,(前面应为奇数否则除以2后会相等,0是特殊情况)p1==0也可
  ```

  获取`func4`的反汇编代码:

  ```assembly
  Dump of assembler code for function func4:                         0x0000000000400fce <+0>:     sub    $0x8,%rsp                    
  0x0000000000400fd2 <+4>:     mov    %edx,%eax                    
  0x0000000000400fd4 <+6>:     sub    %esi,%eax                    
  0x0000000000400fd6 <+8>:     mov    %eax,%ecx                    
  0x0000000000400fd8 <+10>:    shr    $0x1f,%ecx                   
  0x0000000000400fdb <+13>:    add    %ecx,%eax                    
  0x0000000000400fdd <+15>:    sar    %eax                         
  0x0000000000400fdf <+17>:    lea    (%rax,%rsi,1),%ecx           
  0x0000000000400fe2 <+20>:    cmp    %edi,%ecx                    
  0x0000000000400fe4 <+22>:    jle    0x400ff2 <func4+36>          
  0x0000000000400fe6 <+24>:    lea    -0x1(%rcx),%edx              
  0x0000000000400fe9 <+27>:    callq  0x400fce <func4>             
  0x0000000000400fee <+32>:    add    %eax,%eax                    
  0x0000000000400ff0 <+34>:    jmp    0x401007 <func4+57>          
  0x0000000000400ff2 <+36>:    mov    $0x0,%eax                    
  0x0000000000400ff7 <+41>:    cmp    %edi,%ecx                    
  0x0000000000400ff9 <+43>:    jge    0x401007 <func4+57>          
  0x0000000000400ffb <+45>:    lea    0x1(%rcx),%esi               
  0x0000000000400ffe <+48>:    callq  0x400fce <func4>             
  0x0000000000401003 <+53>:    lea    0x1(%rax,%rax,1),%eax        
  0x0000000000401007 <+57>:    add    $0x8,%rsp                    
  0x000000000040100b <+61>:    retq                            
  End of assembler dump.
  ```

  

  

### Phase_5

***

* `rbx`和`rbp`是`callee-saved register`，`P -invoke-> Q `, Q的保护责任

* 反汇编得到`phase_5`的代码

  ```assembly
  Dump of assembler code for function phase_5:                       0x0000000000401062 <+0>:     push   %rbx                         
  0x0000000000401063 <+1>:     sub    $0x20,%rsp                   
  0x0000000000401067 <+5>:     mov    %rdi,%rbx                    
  0x000000000040106a <+8>:     mov    %fs:0x28,%rax                
  0x0000000000401073 <+17>:    mov    %rax,0x18(%rsp)              
  0x0000000000401078 <+22>:    xor    %eax,%eax                    
  0x000000000040107a <+24>:    callq  0x40131b <string_length>     
  0x000000000040107f <+29>:    cmp    $0x6,%eax                    
  0x0000000000401082 <+32>:    je     0x4010d2 <phase_5+112>       
  0x0000000000401084 <+34>:    callq  0x40143a <explode_bomb>      
  0x0000000000401089 <+39>:    jmp    0x4010d2 <phase_5+112>       
  0x000000000040108b <+41>:    movzbl (%rbx,%rax,1),%ecx           
  0x000000000040108f <+45>:    mov    %cl,(%rsp)                   
  0x0000000000401092 <+48>:    mov    (%rsp),%rdx                  
  0x0000000000401096 <+52>:    and    $0xf,%edx                    
  0x0000000000401099 <+55>:    movzbl 0x4024b0(%rdx),%edx          
  0x00000000004010a0 <+62>:    mov    %dl,0x10(%rsp,%rax,1)        
  0x00000000004010a4 <+66>:    add    $0x1,%rax                    
  0x00000000004010a8 <+70>:    cmp    $0x6,%rax                    
  0x00000000004010ac <+74>:    jne    0x40108b <phase_5+41>        
  0x00000000004010ae <+76>:    movb   $0x0,0x16(%rsp)              
  0x00000000004010b3 <+81>:    mov    $0x40245e,%esi               
  0x00000000004010b8 <+86>:    lea    0x10(%rsp),%rdi              
  0x00000000004010bd <+91>:    callq  0x401338 <strings_not_equal>             
  0x00000000004010c2 <+96>:    test   %eax,%eax                    
  0x00000000004010c4 <+98>:    je     0x4010d9 <phase_5+119>       
  0x00000000004010c6 <+100>:   callq  0x40143a <explode_bomb>      
  0x00000000004010cb <+105>:   nopl   0x0(%rax,%rax,1)             
  0x00000000004010d0 <+110>:   jmp    0x4010d9 <phase_5+119>       
  0x00000000004010d2 <+112>:   mov    $0x0,%eax                    
  0x00000000004010d7 <+117>:   jmp    0x40108b <phase_5+41>        
  0x00000000004010d9 <+119>:   mov    0x18(%rsp),%rax              
  0x00000000004010de <+124>:   xor    %fs:0x28,%rax                
  0x00000000004010e7 <+133>:   je     0x4010ee <phase_5+140>       
  0x00000000004010e9 <+135>:   callq  0x400b30 <__stack_chk_fail@plt>         
  0x00000000004010ee <+140>:   add    $0x20,%rsp                   
  0x00000000004010f2 <+144>:   pop    %rbx                         
  0x00000000004010f3 <+145>:   retq                             
  End of assembler dump.
  ```

  注意到`rsp+0x18`这个位置存储了我们**金丝雀**的值这是为了防止**缓冲区溢出**随后调用`string_length` 函数来判断输入的字符串长度。

  由`<+29>:    cmp    $0x6,%eax `可知有6个参数

  等价语句处理：约定p==address, *p\==[address]

  ```C++
  <phase_5+32>:
  goto: <phase_5+112>;
  eax=0;//rax==0
  goto: <phase_5>+41;
  //movzbl (%rbx,%rax,1),%ecx -> 零扩展指令(1byte->4byte)
  loop:
  <+41>:
      ecx=[rbx+rax];
      rdx=cl;//%cl -> (%rsp) --> %rdx;
      edx&=0xf;
      movzbl 0x4024b0(%rdx),%edx;
      dl --> [%rsp+%rax+0x10];
      rax += 1;
  if(rax-6!=0)
      goto: <+41>;
  else//rax==6
  {
      [rsp+0x16]=0;
      esi=0x40245e;
      rdi=rsp+0x10;
      strings_not_equal(rdi,esi);
      if(eax==0)
         goto:<+119>;
      else bomb();
  <+119>:
      rax=[rsp+0x18];
      xor fs:0x28,rax;//金丝雀检查
      if(zf==1) <+140>;
      else call <_stack_chk_fail>;
  }
  return;
  ```
  
  关键代码1
  
  ```assembly
  ;;判断部分
  0x00000000004010ae <+76>:    movb   $0x0,0x16(%rsp)                  
  0x00000000004010b3 <+81>:    mov    $0x40245e,%esi     ;对比参数      
  0x00000000004010b8 <+86>:    lea    0x10(%rsp),%rdi                  
  0x00000000004010bd <+91>:    callq  0x401338 <strings_not_equal>     
  0x00000000004010c2 <+96>:    test   %eax,%eax                        
  0x00000000004010c4 <+98>:    je     0x4010d9 <phase_5+119>           
  0x00000000004010c6 <+100>:   callq  0x40143a <explode_bomb>       
  ```
  
  注意到和`phase_1`相同部分(判断字符串是否相同)
  
  ```assembly
  0x00000000004010bd <+91>:    callq  0x401338 <strings_not_equal>     
  0x00000000004010c2 <+96>:    test   %eax,%eax                        
  0x00000000004010c4 <+98>:    je     0x4010d9 <phase_5+119>           
  0x00000000004010c6 <+100>:   callq  0x40143a <explode_bomb>    
  ```
  
  对于此段，我们查看参数`%esi(parameter 2)`的值
  
  > (gdb) x 0x40245e
  
  ```assembly
  0x40245e:       "flyers"
  ```
  
  关键代码2：注意到循环体`<+41> -> <+74>`
  
  ```assembly
  ;;循环处理
  0x000000000040108b <+41>:    movzbl (%rbx,%rax,1),%ecx ;ecx=[rbx+rax]
  0x000000000040108f <+45>:    mov    %cl,(%rsp)                       
  0x0000000000401092 <+48>:    mov    (%rsp),%rdx ;rdx=cl
  0x0000000000401096 <+52>:    and    $0xf,%edx ;edx只保留dl的低4-bit部分即char
  0x0000000000401099 <+55>:    movzbl 0x4024b0(%rdx),%edx    
  0x00000000004010a0 <+62>:    mov    %dl,0x10(%rsp,%rax,1)            
  0x00000000004010a4 <+66>:    add    $0x1,%rax                        
  0x00000000004010a8 <+70>:    cmp    $0x6,%rax                        
  0x00000000004010ac <+74>:    jne    0x40108b <phase_5+41> ;循环标志
  ```
  
  且由开头代码
  
  ```assembly
  0x0000000000401063 <+1>:     sub    $0x20,%rsp                   
  0x0000000000401067 <+5>:     mov    %rdi,%rbx  
  ;;加入金丝雀代码 %fs:0x28 --> [%rsp + 0x18]
  0x000000000040106a <+8>:     mov    %fs:0x28,%rax                
  0x0000000000401073 <+17>:    mov    %rax,0x18(%rsp)              
  0x0000000000401078 <+22>:    xor    %eax,%eax 
  ```
  
  <img src="D:\github\CSAPP\images\lab2_phase_2_4.png" alt="lab2_phase_2_4" style="zoom:30%;" />
  
  由开头代码可得
  
  1. 0x18开头的8 bytes塞入了`canary`
  2. `%rdi (input string header address) --> %rbx`，基址寄存器`rbx`装入的是输入字符串的首地址
  
  由循环体得到
  
  1. rax从0~5，且ecx依次装入六个`char`（实际上在cl中存储)
  
  2. `<+55>~<+62>`比较有趣：
  
     ```assembly
     //从0x10(%rsp)开始一个字节一个字节的偏移存放该char
     edx = [0x4024b0+%rdx]
     dl -> 0x10(%rsp,%rax,1)
     
     //最后一句给从0x10(%rsp)开始的字符串(6个char)填上终止符(在0x16(%rsp)处)
     0x00000000004010ae <+76>:    movb   $0x0,0x16(%rsp)
     ```
  
  3. 注意到`0x4024b0`,查看
  
     ```
     (gdb) x /s 0x4024b0
     0x4024b0 <array.3449>:  "maduiersnfotvbylSo you think you can stop the bomb with ctrl-c, do you?"
     ```
  
  4. 考虑到结果是`flyers`,可以逆推:
  
     ```
     newChar		offset		Hex		ASCII
     f			9			9		I(0x49)
     l			15			f		O(0x4F)
     y			14			e		N(0x4E)
     e			5			5		E(0x45)
     r			6			6		F(0x46)
     s			7			7		W(0x57)
     ```
  
  5. 注意到
  
     ```assembly
     0x0000000000401096 <+52>:    and    $0xf,%edx ;edx只保留dl的低4-bit部分即char
     ```
  
     因此只需要后四位为相应数即可(答案不唯一):
  
     ```
     IONEFW
     ```
  
     

### Phase_6

***

































































































