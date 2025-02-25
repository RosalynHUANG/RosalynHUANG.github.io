---
layout: post
title: C#中的委托和事件
---

### 委托
官方定义如下：委托定义了一种引用类型，表示对具有特定参数列表和返回类型的方法的引用。
这个定义有点抽象，拆解一下，我们先来表示一个`具有特定参数列表和返回类型的方法`, 如下：
```csharp
public void ThresholdReached(object sender, ThresholdReachedEventArgs e);
```
很容易看出，这个方法的返回类型是`void`，参数列表是`object sender, ThresholdReachedEventArgs e`。
那么如何来表示这种方法的引用呢，简单，在这基础上加一个`delegate`关键字
```csharp
public delegate void ThresholdReachedEventHandler(object sender, ThresholdReachedEventArgs e); 
```
这样我们就定义了一个委托类型，这个委托类型可以引用返回类型是`void`，参数列表是`object sender, ThresholdReachedEventArgs e`的方法。
接下来，我们就可以使用这个委托类型来引用方法了，如下：
```csharp
ThresholdReachedEventHandler handle = ThresholdReached;
```
如果我们调用`handle`，就会调用`ThresholdReached`方法
```csharp
handle?.Invoke(sender, e);  // 等价于 ThresholdReached(sender, e);
```

### 事件
.Net 中的事件基于委托模型，事件是由对象发送的用于发出操作信号的消息。
声明一个事件：
```csharp
\\ 声明事件
public event EventHandler ThresholdReached;

protected virtual void OnThresholdReached(EventArgs e)
    {
        EventHandler handler = ThresholdReached;
        handler?.Invoke(this, e);       // 触发事件
    }
```
.NET 提供了 EventHandler 和 EventHandler<TEventArgs> 委托来支持大部分事件场景

`EventArgs`是所有事件数据类的基类
如何自定义一个数据类：
```csharp
public class ThresholdReachedEventArgs : EventArgs
{
    public int Threshold { get; set; }
    public DateTime TimeReached { get; set; }
}
```

事件处理：
```csharp
class Program
{
    static void Main()
    {
        var c = new Counter();
        c.ThresholdReached += c_ThresholdReached;
        // provide remaining implementation for the class
    }

    static void c_ThresholdReached(object sender, EventArgs e)
    {
        Console.WriteLine("The threshold was reached.");
    }
}
```
