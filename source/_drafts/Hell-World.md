---
title: Hello World程序样例
Tags: code, instances 
---

# Hello World程序样例

以下是用不同语言写成的 **HelloWorld** 程序的列表： ![[Hello
World](/Hello_World_App.png "wikilink")的正确输出示范](Hello_World_App.png "Hello World的正確輸出示範")

## 打印到终端

### ActionScript

``` actionscript
trace("Hello, world!");
```

### Ada

``` ada
with TEXT_IO;
 
procedure HELLO is
begin
    TEXT_IO.PUT_LINE ("Hello, world!");
end HELLO;
```

### 汇编语言

#### x86 CPU，GNU/Linux，NASM {#x86_cpugnulinuxnasm}

``` asm
     section .data
 msg     db      'Hello, world!',0xA
 len     equ     $-msg
 
     section .text
 global  _start
 _start:
         mov     edx,len
         mov     ecx,msg
         mov     ebx,1
         mov     eax,4
         int     0x80
 
         mov     ebx,0
         mov     eax,1
         int     0x80
```

#### x86 AT&T、Gas

    .data
            msg : .string "Hello, world!\n"
            len = . - msg
    .text
    .global _start
    
    _start:
            movl $len, %edx
            movl $msg, %ecx
            movl $1  , %ebx
            movl $4  , %eax
            int $0x80
    
            movl $0  , %ebx
            movl $1  , %eax
            int $0x80

#### x86 CPU、Windows、MASM32

``` ASM
 .386
        .model   flat,stdcall
        option   casemap:none
;==========================================================
    include     windows.inc
    include     user32.inc
    includelib  user32.lib
    include     kernel32.inc
    includelib  kernel32.lib
;==========================================================
        .data
szCaption db "A MessageBox!", 0
szText db "Hello, world!", 0
;==========================================================
        .code
start:
    invoke   MessageBox, NULL, addr szText, addr szCaption, MB_OK
    invoke   ExitProcess, NULL
;==========================================================
    end  start
```

#### 8086作業系統（NASM）

``` asm
[BITS 16]
org 0x7c00
     mov ax,cs
     mov ds,ax
     mov es,ax
     call DispStr
     jmp $;End Hear
DispStr:
     mov ax, BootMessage
     mov bp, ax
     mov cx, 16;How long is the String
     mov ax, 0x1301
     mov bx, 0x000c
     mov dl, 0
     int 0x10
     ret
BootMessage: db "Hello, world!"
times 510-($-$$) db 0x0
dw 0xaa55; Bootable Mark
```

### AutoIt

``` autoit
MsgBox(1,'','Hello, world!')
```

### AWK

``` text
BEGIN { print "Hello, world!" }
```

### Bash (或类似shell)

``` bash
 echo 'Hello, world!'
```

或者：

``` bash
 printf 'Hello, world!\n'
```

### BASIC

**传统版 BASIC（例如 GWBASIC）：**

``` qbasic
10 PRINT "Hello, world!"
20 END
```

或：

``` qbasic
10 PRINT "Hello, world!"
```

或在提示符輸入：

``` qbasic
? "Hello, world!"
```

**现代版 BASIC（例如 Quick BASIC）：**

``` qbasic
Print "Hello, world!"
```

以下的语句，在 Quick BASIC 中同样有效：

``` qbasic
? "Hello, world!"
```

### BCPL

``` text
GET "LIBHDR"

LET START () BE
$(
    WRITES ("Hello, world!*N")
$)
```

### Brainfuck

``` bf
++++++++++[>+++++++>++++++++++>+++>+<<<<-]
>++.>+.+++++++..+++.>++.<<+++++++++++++++.
>.+++.------.--------.>+.>.
```

### BlitzBasic

``` BlitzBasic
Print "Hello, world!"
WaitKey
```

### BOO

``` python
print "Hello, world!"
```

### C

``` c
#include <stdio.h>
int main(void)
{
   printf("Hello, world!\n");
   return 0;
}
```

或者：

``` c
#include <stdio.h>
int main(void)
{
   puts("Hello, world!");
   return 0;
}
```

### CoffeeScript

``` coffeescript
console.log 'Hello, world!'
```

或者：

``` coffeescript
alert 'Hello, world!'
```

### C++ {#c_1}

``` cpp
#include <iostream>
int main()
{
    std::cout << "Hello, world!" << std::endl;
    return 0;
}
```

或者：

``` cpp
#include <iostream>
using namespace std;
int main()
{
    cout << "Hello, world!" << endl;
    return 0;
}
```

### C++/CLI

``` cpp
int main()
{
    System::Control::WriteLine("Hello, world!");
}
```

### C# (C Sharp) {#c_c_sharp}

``` csharp
using System

class HelloWorldApp
{
    static void Main(string[] args)
    {
        Console.WriteLine("Hello, world!");
    }
}
```

或者（仅用于Microsoft Windows）

``` csharp
class HelloWorldApp
{
     [DllImport("user32.dll")]
     static extern MessageBox(string title, string message);
     public static void Main()
     {
          MessageBox(null, "Hello, world!");
     }
}
```

或者（使用附加的Windows Forms）

``` csharp
using System.Windows.Forms;
class HelloWorldApp
{
     public static void Main()
     {
          MessageBox.Show("Hello, world!");
     }
}
```

### COBOL

``` cobol
      IDENTIFICATION DIVISION.
      PROGRAM-ID. HELLO-WORLD.

      ENVIRONMENT DIVISION.

      DATA DIVISION.

      PROCEDURE DIVISION.
      DISPLAY "Hello, world!".
      STOP RUN.
```

### Common Lisp {#common_lisp}

``` lisp
;直接輸出
"Hello world!"
 
;或者
(format t "Hello world!~%")
```

### DOS批处理

``` dos
@echo Hello, world!
```

对于MS-DOS 3.0或更低版本：

``` dos
echo off
cls
echo Hello, world!
```

### Linux Shell {#linux_shell}

``` dos
echo Hello, world!
```

### Eiffel

``` eiffel
class HELLO_WORLD

creation
    make
feature
    make is
    local
        io:BASIC_IO
    do
        !!io
        io.put_string("%N Hello, world!")
    end -- make
end -- class HELLO_WORLD
```

### Erlang

``` text
    -module(hello).
    -export([hello_world/0]).

    hello_world() -> io:fwrite("Hello, World!\n").
```

### Flowgorithm

```{=mediawiki}
{{Main|Flowgorithm (programming language)}}
```

[`file:Flowgorithm Hello World.png`](file:Flowgorithm_Hello_World.png "wikilink")

### Forth

``` text
." Hello, world!" CR
```

### Fortran

``` fortran
    WRITE(*,*) 'Hello, world!'
    STOP
    END
```

### Go

``` go
package main

import "fmt"

func main() {
    fmt.Println("Hello, world!")
}
```

### HQ9+

``` hq9plus
Hello World
```

### INTERCAL

``` intercal
PLEASE DO ,1 <- #13
DO ,1 SUB #1 <- #238
DO ,1 SUB #2 <- #112
DO ,1 SUB #3 <- #112
DO ,1 SUB #4 <- #0
DO ,1 SUB #5 <- #64
DO ,1 SUB #6 <- #238
DO ,1 SUB #7 <- #26
DO ,1 SUB #8 <- #248
DO ,1 SUB #9 <- #168
DO ,1 SUB #10 <- #24
DO ,1 SUB #11 <- #16
DO ,1 SUB #12 <- #158
DO ,1 SUB #13 <- #52
PLEASE READ OUT ,1
PLEASE GIVE UP
```

### Java

``` java
public class Hello
{
    public static void main(String[] args)
    {
        System.out.print("Hello, world!");
    }
}
```

### JavaScript

该代码适用于浏览器控制台以及[Node.js](Node.js "wikilink")等服务器端运行环境。

``` javascript
console.log("Hello, World!");
```

### Julia

``` julia
println("Hello, world!") 
```

### Lisp

``` lisp
;直接输出
"hello, world"

;或者
(format t "hello, world~%")
```

### Lua

``` lua
print "Hello, world!"
```

### Malbolge

    (=<`#9]~6ZY327Uv4-QsqpMn&+Ij"'E%e{Ab~w=_:]Kw%o44Uqp0/Q?xNvL:`H%c#DD2^WV>gY;dts76qKJImZkj

### Matlab

``` matlab
disp('hello world!')
```

### Mathematica

` Hello[] := Print["``<span style="color:grey">`{=html}`Hello World!``</span>`{=html}`"]`\
` Hello[]`

### Metapost

``` text
beginfig(1);
draw (0,0)--(0,10);
draw (0,5)--(5,5);
draw (5,0)--(5,10);
draw (12,0)--(7,0)--(7,10)--(12,10);
draw (12,5)--(7,5);
draw (14,10)--(14,0)--(19,0);
draw (21,10)--(21,0)--(26,0);
draw (28,5)...(30.5,0)...(33,5)...(30.5,10)...cycle;
draw (38,10)--(39.25,0)--(40.5,10)--(41.75,0)--(43,10);
draw (45,5)...(47.5,0)...(50,5)...(47.5,10)...cycle;
draw (52,0)--(52,10);
draw (52,10)..(57,4)..(52,6.5);
draw (52,5)--(57,0);
draw (61,10)--(61,0)--(66,0);
draw (68,10)--(68,0)..(73,5)..cycle;
endfig;
end
```

### MIXAL

``` text
TERM EQU 19 the MIX console device number
        ORIG   1000        start address
START OUT MSG(TERM) output data at address MSG
        HLT                halt execution
MSG ALF "MIXAL"
        ALF    " HELL"
        ALF    "O WOR"
        ALF    "LD   "
        END    START       end of the program
```

### Nuva

``` text
<..直接输出..>
Hello, world!

<..或者..>

<.
  // 不带换行
  ?  "Hello, world!"

  // 或者

  // 带换行
  ?? 'Hello, world!'
.>
```

### Objective-C {#objective_c}

``` objc
#import <Foundation/Foundation.h>

int main(int argc, const char * argv[])
{
    @autoreleasepool {
        NSLog(@"Hello, World!");
    }
    return 0;
}
```

### OCaml

``` ocaml
let main () =
    print_endline "Hello world!";;
```

### Pascal

``` pascal
program Hello;{此行可以省略}
begin
    writeln('Hello, world!');
end.
```

### Perl

``` perl
#!/usr/bin/env perl
print "Hello, world!\n";
```

Perl 5.10（含）以後版本:

``` perl
#!/usr/bin/env perl
use 5.010;
say "Hello, world!";
```

### Pike

``` text
#!/usr/local/bin/pike
int main()
{
    write("Hello, world!\n");
    return 0;
}
```

### PL/I

``` text
Test: procedure options(main);
    declare My_String char(20) varying initialize('Hello, world!');
    put skip list(My_String);
end Test;
```

### Prolog

``` prolog
goal
    write("hello,world!").
```

### Python

適用於Python 2：

``` python
#!/usr/bin/env python
print "Hello, world!"
```

適用於Python 2.6, 2.7, 3：

``` python
#!/usr/bin/env python
print("Hello, world!")
```

用[彩蛋](彩蛋_(媒体) "wikilink")输出Hello World：

``` python
#!/usr/bin/env python
import __hello__
```

### REXX

``` text
say "Hello, world!"
```

### Ruby

``` ruby
#!/usr/bin/ruby
puts "Hello, world!"
```

### Rust

``` rust
fn main() {
    println!("Hello, world!");
}
```


=== [Sbyke Laborana](Sbyke_Laborana "wikilink") ===

``` text
INIT min:1001
Om:"Hello, world!"
```

### Scheme

``` scheme
(display "Hello, world!")
(newline)
```

### Scratch

```{=mediawiki}
{{Main|Scratch (程式語言)}}
```

`<small>`{=html}`</small>`{=html}![`Scratch_Hello_World_zh_hant.png`](/Scratch_Hello_World_zh_hant.png "Scratch_Hello_World_zh_hant.png")

### sed

（需要至少一行输入）

``` text
sed -ne '1s/.*/Hello, world!/p'
```

### Seed7

``` text
$ include "seed7_05.s7i";

  const proc: main is func
    begin
      writeln("Hello, world!");
    end func;
```

### Smalltalk

``` smalltalk
Transcript show: 'Hello, world!'
```

### Small Basic {#small_basic}

    TextWindow.WriteLine("Hello, world!")

### SNOBOL

``` text
    OUTPUT = "Hello, world!"
END
```

### SQL

#### 第一種

``` sql
create table MESSAGE (TEXT char(15));
insert into MESSAGE (TEXT) values ('Hello, world!');
select TEXT from MESSAGE;
drop table MESSAGE;
```

#### 第二種

``` sql
select 'hello, world';
```

#### 第三種

``` sql
print 'hello,world!'
```

### Swift

適用於Swift 1.x：

    println("Hello, World!")

適用於Swift 2.x, 3：

    print("Hello, World!")

### Tcl

``` tcl
#!/usr/local/bin/tcl
puts "Hello, world!"
```

### TScript

``` text
? "Hello, world!"
```

### Turing

``` text
put "Hello, world!"
```

### UNIX-style shell {#unix_style_shell}

程序中的/bin/sh可改为您使用的[shell](shell "wikilink")

``` bash
#!/bin/sh
echo 'Hello, world!'
```

#### bc

    #!/usr/bin/bc -q
    print "Hello World"
    quit

#### dc

    #!/usr/bin/env dc
    [Hello World]p

[^1]: [The Hello World Collection](http://helloworldcollection.de)

    `{{Wayback|url=http://helloworldcollection.de/ |date=20190128212113 }}`{=mediawiki}
    helloworldcollection.de \[2014-12-12\]

## 图形用户界面

### AppleScript

``` AppleScript
display dialog "Hello, world!"
```

或者：

``` AppleScript
display alert "Hello, world!"
```

### Delphi

``` delphi
program HelloWorld;
uses
    Dialogs;
begin
    ShowMessage('Hello, World!');
end.
```

### Nuva

``` text
<.
  System.Ui.ShowMessage('Nuva', 'Hello, world!', ['OK'])
.>
```

### Visual Basic {#visual_basic}

``` vb
Sub Main()
    MsgBox "Hello, world!"
End Sub
```

### Visual FoxPro {#visual_foxpro}

``` visualfoxpro
? "Hello, world!"
```

### X11

用一个程序

``` text
xmessage 'Hello, world!'
```

使用Qt

``` cpp
#include <QApplication>
#include <QLabel>

int main(int argc, char *argv[])
{
    QApplication app(argc, argv);
    QLabel label("Hello, world!");
    label.show();
    return app.exec();
}
```

C 和 GTK+

``` C
#include <gtk/gtk.h>

int main(int argc, char * args[])
{
    GtkWidget * win, * label;
    gtk_init(& argc, & args);

    label = gtk_label_new("Hello, world!");
    win = gtk_window_new(GTK_WINDOW_TOPLEVEL);
    gtk_container_add(GTK_CONTAINER(win), label);
    gtk_widget_show_all();

    gtk_main();

    return 0;
}
```

用C++和gtkmm 2

``` cpp
#include <iostream>
#include <gtkmm/main.h>
#include <gtkmm/button.h>
#include <gtkmm/window.h>
using namespace std;

class HelloWorld : public Gtk::Window 
{
public:
    HelloWorld();
    virtual ~HelloWorld();
protected:
    Gtk::Button m_button;
    virtual void on_button_clicked();
};

HelloWorld::HelloWorld() : m_button("Hello, world!")
{
    set_border_width(10);
    m_button.signal_clicked().connect(SigC::slot(*this, &HelloWorld::on_button_clicked));
    add(m_button);
    m_button.show();
}

HelloWorld::~HelloWorld() {}

void HelloWorld::on_button_clicked() 
{
    cout << "Hello, world!" << endl;
}

int main（int argc, char *argv[]）
{
    Gtk::Main kit(argc, argv);
    HelloWorld helloworld;
    Gtk::Main::run(helloworld);
}
```

### Java

``` java
import java.awt.*;
import java.awt.event.*;

public class HelloFrame extends Frame 
{
    HelloFrame(String title) 
    {
        super(title);
    }
    public void paint(Graphics g)
    {
        super.paint(g);
        java.awt.Insets ins = this.getInsets();
        g.drawString("Hello, World!", ins.left + 25, ins.top + 25);
    }
    public static void main(String args [])
    {
        HelloFrame fr = new HelloFrame("Hello");

        fr.addWindowListener(
            new WindowAdapter() 
            {
                public void windowClosing(WindowEvent e)
                {
                    System.exit( 0 );
                }
            }
        );
        fr.setResizable(true);
        fr.setSize(500, 100);
        fr.setVisible(true);
    }
}
```

### Java Applet {#java_applet}

Java Applet用于HTML文件。

HTML代码：

``` html4strict
<html>
<head>
<title>Hello World</title>
</head>
<body>
HelloWorld Program says:
<applet code="HelloWorld.class" width="600" height="100">
</applet>
</body>
</html>
```

Java代码：

``` java
import java.applet.*;
import java.awt.*;

public class HelloWorld extends Applet 
{
    public void paint(Graphics g) 
    {
        g.drawString("Hello, world!", 100, 50);
    }
}
```

### JavaScript

[JavaScript](JavaScript "wikilink")是一种脚本语言。最广泛用于HTML文件中，也可以用在其它宿主环境下，比如Microsoft®
Windows® 脚本宿主（[WSH](WSH "wikilink")）和一些web服务环境。

用于HTML中：

``` javascript
// 弹出对话框
alert("Hello, World!");
// 在页面上显示
document.write("Hello, World!");
```

用于WSH中：

``` javascript
WScript.Echo("Hello, World!");
```

作为服务器端（Node.js）：启动后，需要在浏览器中访问"[<http://127.0.0.1:8000/>](http://127.0.0.1:8000/)"查看。

``` javascript
const http = require('http');

http.createServer((request, response) => {
  response.writeHead(200, { 'Content-Type': 'text/plain' });
  response.end('Hello World!');
}).listen(8000);

console.log('Server running at http://127.0.0.1:8000/');
```

### PostScript

PostScript是一种专门用来创建图像的语言，常用于打印机。

``` text
/font /Courier findfont 24 scalefont
font setfont
100 100 moveto
(Hello World!) show
showpage
```

### SPARQL

``` sparql
SELECT ?h WHERE { 
  VALUES ?h { "Hello World" } 
}
```

### XAML

``` xml
 <Page
  xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
  xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
  x:Class="XAMLSample.Page1"
 >
  <Button Click="HelloWorld" Name="Button1">Click Here</Button>
 </Page>
```

接著使用 C# 建置

``` csharp
 using System;
 using System.Windows;
 using System.Windows.Controls;
 
 namespace XAMLSample
 {
  public partial class Page1 : Page
  {
    void HelloWorld(object sender, RoutedEventArgs e)
    {
      MessageBox.Show("Hello, world!");
    }
  }
 }
```

### 易语言

` .版本 2`\
` .程序集 窗口程序集1`\
` .子程序 __启动窗口_创建完毕`\
` 信息框 (“Hello, World!”, 0, )`

[^1]: [leachim6/hello-world/](https://github.com/leachim6/hello-world/blob/master/s/SparQL.sparql)

    `{{Wayback|url=https://github.com/leachim6/hello-world/blob/master/s/SparQL.sparql |date=20190322182531 }}`{=mediawiki}
    GitHub \[2021-06-20\]

## 服务器端

以下为Web服务端示例程序，程序启动后，需要用浏览器访问特定地址来查看结果。

### ASP

``` asp
<% Response.Write("Hello, world!") %>
```

或者简单地写成：

``` asp
<%= "Hello, world!" %>
```

### HTML

``` html4strict
<!-- 直接輸出... -->
Hello World
<!-- 或者 -->
<html>
<head>
    <title> Hello World </title>
</head>
<body>
Hello World
</body>
</html>
```

### Go

``` go
package main

import (
    "fmt"
    "net/http"
)

func helloHandler(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "hello world")
}

func main() {
    http.HandleFunc("/", helloHandler)
    http.ListenAndServe("0.0.0.0:8000", nil)
}
```

### JSP

``` java
<%
    out.print("Hello, world!");
%>
```

或者简单地写成：

``` java
<%="Hello, world!"%>
```

### JavaScript

以下为Node.js环境：

``` javascript
const http = require('http');

http.createServer((request, response) => {
  response.writeHead(200, { 'Content-Type': 'text/plain' });
  response.end('Hello World!');
}).listen(8000);

console.log('Server running at http://127.0.0.1:8000/');
```

### PHP

以下代码既可以作为服务器端运行，显示在用户浏览器上，也可以在终端中直接运行，输出到终端中：

``` php
<?php
    echo 'Hello, world!';
    // 或者
    print 'Hello, world!';
?>
```

或者

``` php
<?= "Hello World!"?>
```
