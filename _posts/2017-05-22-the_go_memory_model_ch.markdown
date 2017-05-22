---
layout: post
title:  "The Go Memory Model 中文翻译"
date:   2017-05-22 16:00:00 +0800
categories: Go
---
原文：[The Go Memory Model](https://golang.org/ref/mem)
# Go内存模型

## 版本：2014年5月31日

## 介绍
本篇文章介绍了，在什么情况下可以保证，在一个goroutine中变量的读，能观察到，在另一个goroutine中同一变量写的值。
## 忠告
修改同时被多个goroutine访问的数据的程序必须要顺序化这些访问。

为了顺序化访问，要用channel操作或者其他同步原语，比如sync和sync/atomic包。

如果你必须读这篇文档来理解你的程序的行为，那么你“太聪明“了。

不要“太聪明”。
## 之前发生
在一个单独的goroutine中，读和写必须表现得和它们在程序中指定的一致。就是说，编译器和处理器可以重新排列读写操作，只要在这个goroutine中，语言规范定义行为没有被改变。由于重新排列，一个goroutine观察到的执行顺序可能和另一个观察到的不一样。比如，一个goroutine执行 a = 1; b = 2;，另一个可能观察到b的值的更新在a之前。

为了指定读写的要求，我们定义Go程序的内存操作的一个部分顺序：“之前发生”。如果事件e1在e2之前发生，那么e2在e1之后发生。如果e1没有在e2之前发生，也没有在e2之后发生，那我们说e1和e2同时发生。

在单个goroutine中，”之前发生“顺序是程序表示的顺序。

如果下面两个条件都满足，变量v的读r可以观察到v的写w：

1. r没有在w之前发生。
2. 在w之后r之前，没有其他的写w'发生。

为了保证变量v的读能观察到v的特定写w，请确保w是r唯一允许观察的写。就是说，如果以下两个条件都满足，就能保证r能观察w：

1. w在r之前发生。
2. 任何其他对共享变量r的写，发生在w之前或者r之后。

这两个条件比之前两个更严格，要求没有其他的写和w或r同时发生。

在单个goroutine中，没有并发，所以这两个是一样的：读r观察到最近的对v的写w。当多个goroutine访问共享的变量r，它们必须使用同步事件来建立“之前发生”条件，以确保读能观察到所需的写。

变量v的零值初始化行为，和内存模型的写一致。

大于单个机器字节的变量的读写和多个机器字节操作一样，是不定顺序的。
## 同步
### 初始化

程序初始化是在单个goroutine下跑的，但是该goroutine可能会创建其他goroutine，从而并发。

如果package p导入package q，q的init方法在p的任一init方法开始前发生。

main.main方法在所有init方法完成后开始。
### Goroutine创建
go语句开始一个新的goroutine并开始执行。

比如，在这个程序里：

```go
var a string

func f() {
	print(a)
}

func hello() {
	a = "hello, world"
	go f()
}
```
调用 hello 将会在未来某个时刻打印 "hello, world"（也许在hello返回后）。
### Goroutine析构
gotoutine的退出不保证发生在程序的任何事件之前。比如，在这个程序里：

```go
var a string

func hello() {
	go func() { a = "hello" }()
	print(a)
}
```
a的赋值没有在任何同步事件后，所以不保证被其他goroutine观察到。事实上，一个任性的编译器可能会删掉整个go语句。

如果goroutine的影响必须被另一个goroutine观察到，请使用一个同步机制比如锁或者channel通信来建立一个相对的顺序。
### Channel通信
Channel通信是goroutine之间主要的同步方法。在特定channel上的每个send都与该channel上对应的receive匹配，receive通常在另一个goroutine中。

一个channel上的send在这个channel上对应的receive完成之前发生。

这个程序：

```go
var c = make(chan int, 10)
var a string

func f() {
	a = "hello, world"
	c <- 0
}

func main() {
	go f()
	<-c
	print(a)
}
```
保证打印"hello, world"。a的写在c的send之前发生，send则在c上对应的receive完成之前发生，receive则在print之前发生。

channel的关闭在receive之前发生，因为channel被关闭了，receive返回一个零值。

在前面这个例子里，把 c <- 0 换成 close(c) 后，程序的行为依然保证是一样的。

一个无缓冲的channel的receive在这个channel的send完成之前发生。

这个程序（除了send和receive语句换了一下，并使用无缓冲的channel，其他和上面的一样）：

```go
var c = make(chan int)
var a string

func f() {
	a = "hello, world"
	<-c
}

func main() {
	go f()
	c <- 0
	print(a)
}
```
也保证打印"hello, world"。对a的写在c的receive之前发生，receive在c上对应的send完成之前发生，send在print之前发生。

如果channel是有缓冲的（比如， c = make(chan int, 1)），那么程序不保证打印"hello world"。（可能会打印空字符串、崩溃或者什么都不做。）

在一个容量为C的channel上的第k次receive在该channel上第k+C次send完成之前发生。

这个规则归纳出了前面有缓冲的channel的规则。可以使用一个有缓冲的channel来模拟一个计数信号量：channel里的项数对应于可用数，channel的容量对应于最大并发数，发送一项就是获取信号量，接受一项就是释放信号量。这是限制并发的常见方法。

这个程序为work列表中的每项启动一个goroutine，不过这些goroutine使用一个limit channel来保证最多三个work方法在同时运行。

```go
var limit = make(chan int, 3)

func main() {
	for _, w := range work {
		go func(w func()) {
			limit <- 1
			w()
			<-limit
		}(w)
	}
	select{}
}
```
### Locks
sync包实现了两种lock数据类型，sync.Mutex和sync.RWMutex。

对于每个sync.Mutex或sync.RWMutex变量l，n < m，第n次l.Unlock()在第m次l.Lock()返回之前发生。

这个程序：

```go
var l sync.Mutex
var a string

func f() {
	a = "hello, world"
	l.Unlock()
}

func main() {
	l.Lock()
	go f()
	l.Lock()
	print(a)
}
```
保证打印"hello, world"。第一次调用l.Unlock()（在f里）在第二次调用l.Lock()（在main里）返回之前发生，第二次l.Lock在print之前发生。

对于类型sync.RWMutex的变量l的每个l.RLock调用，有一个n可以使得，l.RLock（返回）在第n次l.Unlock之后发生，对应的l.RUnlock在第n+1次l.Lock之前发生。
### Once
sync包通过Once类，提供了一个安全机制，用于多个goroutine中的初始化。多个线程可以为一个特定的f执行once.Do(f)，但是只有一个会运行f()，其他调用将会阻塞，直到f()返回。

once.Do(f)里的单个的f()调用（返回）在任何once.Do(f)返回之前发生。

在这个程序里：

```go
var a string
var once sync.Once

func setup() {
	a = "hello, world"
}

func doprint() {
	once.Do(setup)
	print(a)
}

func twoprint() {
	go doprint()
	go doprint()
}
```
调用twoprint打印两次"hello, world"。首个doprint调用执行setup一次。
### 不正确的同步
注意，读r可以观察到与r同时发生的写w的值。即使发生这种情况，这也不表示在r之后的读能观察到w之前发生的写。

在这个程序里：

```go
var a, b int

func f() {
	a = 1
	b = 2
}

func g() {
	print(b)
	print(a)
}

func main() {
	go f()
	g()
}
```
可能会发生，g先打印2，然后打印0。

这个事实使一些常见的用法无效。

双重锁定试图避免同步的开销。比如，twoprint程序可以错误得写成这样：

```go
var a string
var done bool

func setup() {
	a = "hello, world"
	done = true
}

func doprint() {
	if !done {
		once.Do(setup)
	}
	print(a)
}

func twoprint() {
	go doprint()
	go doprint()
}
```
但这不能保证，在doprint中，观察done的写意味着观察a的写。这个版本可能（不正确的）会打印一个空的字符串而不是"hello, world"。

另一个错误的用法是忙等一个值，比如：

```go
var a string
var done bool

func setup() {
	a = "hello, world"
	done = true
}

func main() {
	go setup()
	for !done {
	}
	print(a)
}
```
和前面一样，不能保证，在main里，观察done的写意味着观察a的写，所以这个程序也可能打印一个空字符串。更糟糕的是还不能保证done的写被main观察到，因为在两个线程间没有同步事件。main中的循环不能保证退出。

这个主题还有一些细微的变体，比如这个程序。
```go
type T struct {
	msg string
}

var g *T

func setup() {
	t := new(T)
	t.msg = "hello, world"
	g = t
}

func main() {
	go setup()
	for g == nil {
	}
	print(g.msg)
}
```
即使main观察到 g != nil 并退出循环，还是不能保证它可以观察到初始化的g.msg的值。

在所有这些例子中，解决方案是相同的：使用显式的同步。
