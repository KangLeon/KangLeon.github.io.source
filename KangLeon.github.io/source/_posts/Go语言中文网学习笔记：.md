---
title: Go语言中文网学习笔记
categories:   
 - Go
tags: 
- Go
- 语言基础
---

## Go语言中文网学习笔记：

#### 1.判断一个字符串是否为空：

	if str == "" { ... }
	
	if len(str) == 0 {...}

#### 2.判断运行 Go 程序的操作系统类型，这可以通过常量 runtime.GOOS 来判断(第 2.2 节)。

		 if runtime.GOOS == "windows"     {
		     .    ..
		 } else { // Unix-like
		     .    ..
		 }

这段代码一般被放在 init() 函数中执行。这儿还有一段示例来演示如何根据操作系统来决定输入结束的提示：

	 var prompt = "Enter a digit, e.g. 3 "+ "or %s to quit."
	
	 func init() {
	     if runtime.GOOS == "windows" {
	         prompt = fmt.Sprintf(prompt, "Ctrl+Z, Enter")        
	     } else { //Unix-like
	         prompt = fmt.Sprintf(prompt, "Ctrl+D")
	     }
	 }

#### 3.函数 Abs() 用于返回一个整型数字的绝对值:

	func Abs(x int) int {
	     if x < 0 {
	         return -x
	     }
	     return x    
	 }

#### 4.isGreater 用于比较两个整型数字的大小:

	 func isGreater(x, y int) bool {
	     if x > y {
	         return true    
	     }
	     return false
	 }
	 
在第四种情况中，if 可以包含一个初始化语句（如：给一个变量赋值）。这种写法具有固定的格式（在初始化语句后方必须加上分号）：
	 
	if initialization; condition {
	    // do something
	}
	
例如:

	val := 10
	if val > max {
	    // do something
	}
	
你也可以这样写:

	if val := 10; val > max {
	    // do something
	}	
	
*但要注意的是，使用简短方式 := 声明的变量的作用域只存在于 if 结构中（在 if 结构的大括号之间，如果使用 if-else 结构则在 else 代码块中变量也会存在）。如果变量在 if 结构之前就已经存在，那么在 if 结构中，该变量原来的值会被隐藏。最简单的解决方案就是不要在初始化语句中声明变量（见 5.2 节的例 3 了解更多)。*
	
下面的代码片段展示了如何通过在初始化语句中获取函数 process() 的返回值，并在条件语句中作为判定条件来决定是否执行 if 结构中的代码：

	if value := process(data); value > max {
	    ...
	}