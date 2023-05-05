---
title: go常见陷阱
date: 2023-05-05 10:58:26
tags: "go"
categories: "go"
---

## 数组和切片

### 数组是值传递

在函数调用参数中，数组是值传递，无法通过修改数组类型的参数返回结果，必要时需要使用切片

```go
func main() {
	x := [3]int{1, 2, 3}

	func(arr [3]int) {
		arr[0] = 7
		fmt.Println(arr)
	}(x)

	fmt.Println(x)
}
```

### 函数参数传递切片

切片实际是一个指向潜在数组的指针，以切片为参数时只需如下：

```go
func findBiggest( listOfNumbers []int ) int {}
```

### 切片会导致整个底层数组被锁定

切片会导致整个底层数组被锁定，底层数组无法释放内存。如果底层数组较大会对内存产生很大的压力。

```go
func main() {
	headerMap := make(map[string][]byte)

	for i := 0; i < 5; i++ {
		name := "/path/to/file"
		data, err := ioutil.ReadFile(name)
		if err != nil {
			log.Fatal(err)
		}
		headerMap[name] = data[:1]
	}

	// do some thing
}

```

解决的方法是将结果克隆一份，这样可以释放底层的数组：

```go
func main() {
	headerMap := make(map[string][]byte)

	for i := 0; i < 5; i++ {
		name := "/path/to/file"
		data, err := ioutil.ReadFile(name)
		if err != nil {
			log.Fatal(err)
		}
		headerMap[name] = append([]byte{}, data[:1]...)
	}

	// do some thing
}
```

## Map

### map遍历是顺序不固定

map是一种hash表实现，每次遍历的顺序都可能不一样。

```go
func main() {
	m := map[string]string{
		"1": "1",
		"2": "2",
		"3": "3",
	}

	for k, v := range m {
		println(k, v)
	}
}
```

## 接口

### 空指针和空接口不等价

比如返回了一个错误指针，但是并不是空的error接口：

```go
func returnsError() error {
	var p *MyError = nil
	if bad() {
		p = ErrBad
	}
	return p // Will always return a non-nil error.
}
```

## 协程

### 不同Goroutine之间不满足顺序一致性内存模型

因为在不同的Goroutine，main函数中无法保证能打印出`hello, world`:

```go
var msg string
var done bool

func setup() {
	msg = "hello, world"
	done = true
}

func main() {
	go setup()
	for !done {
	}
	println(msg)
}

```

解决的办法是用显式同步：

```go
var msg string
var done = make(chan bool)

func setup() {
	msg = "hello, world"
	done <- true
}

func main() {
	go setup()
	<-done
	println(msg)
}

```

msg的写入是在channel发送之前，所以能保证打印`hello, world`

## 其它

### 循环中defer

```go
for _, file := range files {
    if f, err = os.Open(file); err != nil {
        return
    }
    // 这是错误的方式，当循环结束时文件没有关闭
    defer f.Close()
    // 对文件进行操作
    f.Process(data)
}
```

在循环内结尾处的 `defer` 没有执行，所以文件一直没有关闭，不应该使用`defer`

**`defer` 仅在函数返回时才会执行，在循环内的结尾或其他一些有限范围的代码内不会执行**

### recover必须在defer函数中运行

`recover`捕获的是祖父级调用时的异常，直接调用时无效：

```go
func main() {
	recover()
	panic(1)
}
```

直接`defer`调用也是无效：

```go
func main() {
	defer recover()
	panic(1)
}
```

`defer`调用时多层嵌套依然无效：

```go
func main() {
	defer func() {
		func() { recover() }()
	}()
	panic(1)
}
```

必须在`defer`函数中直接调用才有效：

```go
func main() {
	defer func() {
		recover()
	}()
	panic(1)
}

```

### main函数提前退出

后台Goroutine无法保证完成任务。

```go
func main() {
	go println("hello")
}

```

### 通过Sleep来回避并发中的问题

休眠并不能保证输出完整的字符串：

```go
func main() {
	go println("hello")
	time.Sleep(time.Second)
}

```

类似的还有通过插入调度语句：

```go
func main() {
	go println("hello")
	runtime.Gosched()
}
```

### 闭包错误引用同一个变量

```go
func main() {
	for i := 0; i < 5; i++ {
		defer func() {
			println(i)
		}()
	}
}

```

改进的方法是在每轮迭代中生成一个局部变量：

```go
func main() {
	for i := 0; i < 5; i++ {
		i := i
		defer func() {
			println(i)
		}()
	}
}

```

或者是通过函数参数传入：

```go
func main() {
	for i := 0; i < 5; i++ {
		defer func(i int) {
			println(i)
		}(i)
	}
}
```