## 深入了解GO语言中的append函数
#### 这在许多编程语言,经常使用函数添加元素到数组或者数组类型的对象中，比如JavaScript中的`Array.push`，又比如Python中的`List.append`，在GO语言中，附加元素到`slice(切片)`类型对象的函数叫做：`append`,这一点与其他语言是非常相似的。
```
var numbers []int
numbers = append(numbers, 250)
numbers = append(numbers, 2071)
numbers = append(numbers, 1123)
// logs [250, 2071, 1123]
fmt.Println(numbers)
```
### 在上面的代码片段中，我们附加了数字250,2071和1123到int整型切片中，然后打印这个切片的内容

### 在大多数时候，我们简单地认为`apend`函数仅仅是添加元素到切片中而已，然而，当你附加元素到切片中时，了解屏幕后的代码如何运行可以帮助你写好Go代码让程序运行得更快，同时也能避免一些在工作中使用切片的`apend`函数的一些不容易察觉的陷阱。因此在这次教程中，我们将近距离地观察到当你在使用`apend`的时候，你的电脑如何运作的。
## 观察切片的扩容 
### 一个切片包含5个重要的信息
- [ ] 长度;`len(slice)`
- [ ] 容量;`cap(slice)`
- [ ] 里面的元素;`fmt.Sprintf("%v", slice)`
- [ ] 内存地址; `fmt.Sprintf("%p", slice)`
- [ ] 元素类型; `fmt.Sprintf("%T", slice)`
### 首先，让我们观察一下连续往一个切片中附加5个元素时切片的前四个属性发生什么变化
```
var numbers []int
for i := 0; i < 5; i++  {
    numbers = append(numbers, i)
    fmt.Printf("address: %p, length: %d, capacity: %d, items: %v\n",
        numbers, len(numbers), cap(numbers), numbers)
}
```

这是我程序执行后的输出，每一次运行，内存地址都会不一样，但是其他属性应该都是相同的

```
address: 0xc00009a000, length: 1, capacity: 1, items: [0]
address: 0xc00009a030, length: 2, capacity: 2, items: [0 1]
address: 0xc0000a6000, length: 3, capacity: 4, items: [0 1 2]
address: 0xc0000a6000, length: 4, capacity: 4, items: [0 1 2 3]
address: 0xc000092080, length: 5, capacity: 8, items: [0 1 2 3 4]
```
### 注意
- 每一次我们添加元素，`numbers`的长度和`numbers`切片中的元素都会发生改变
- 当切片处于最大容量时，每次`len(numbers)`和`cap(numbers)`都是一样的，当我们添加另外一个元素到一个满容量的切片中时，切片的容量会增长
```
address: 0xc0000a6000, length: 3, capacity: 4, items: [0 1 2]
address: 0xc0000a6000, length: 4, capacity: 4, items: [0 1 2 3]
address: 0xc000092080, length: 5, capacity: 8, items: [0 1 2 3 4]
```
- 不仅如此，当我们往一个满容量的切片中添加元素时，它的地址也会发生改变
>就最后一点来说，这意味着当这个切片添加第5个元素时，你的操作系统会为这个切片分配一个新的内存空间，变量`numbers`的值指向了这片新分配的内存地址。为了更好的理解内存分配的原理，我们需要先去了解切片背后的数据结构:`Go arrays`!
### Go语言中的数组
当你想要实现一个连续的、有序的元素集合，很大概率会用到切片。然而，这并不意味着在Go语言中没有数组这个概念。Go语言支持数组，但是我们并不直接使用它，这是因为数组主要被用于存储切片的底层数据。<br>
数组与切片最大的区别在于当你定义一个数组时，你必须显式地设置数组的大小，比如，你要定义一个两个元素的字符串数组要使用`[2]string`而不是`[]string`:
```
// A slice of strings, with two items in it
vehiclesSlice := []string{"car", "Pokémon that knows SURF"}
// An array with two items in it, with its size set to two
vehiclesArray := [2]string{"car", "Pokémon that knows SURF"}
```
你可以很方便地访问数组或者切片中的元素，比如为了查看`vehicles`中全部的元素，使用 `loop`对于`vehiclesSlice`和`vehiclesArray`同样有效:
