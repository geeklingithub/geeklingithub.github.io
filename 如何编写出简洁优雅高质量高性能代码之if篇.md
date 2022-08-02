
##  if 编码实践
编写出优雅、简洁、易读易维护且不易错的代码
遵循如下原则
- 仅使用单分支控制结构
- 当布尔表达式求值为 false 时，也就是出现错误时，在单分支中快速返回
- 正常逻辑在代码布局上始终“靠左”，这样读者可以从上到下一眼看到该函数正常逻辑的全貌
- 函数执行到最后一行代表一种成功状态
- 命中的概率最多的写前面

优劣示例
//伪代码段1：
```shell
func doSomething() error {
  if errorCondition1 {
    // some error logic
    ... ...
    return err1
  }
  
  // some success logic
  ... ...

  if errorCondition2 {
    // some error logic
    ... ...
    return err2
  }

  // some success logic
  ... ...
  return nil
}
```
// 伪代码段2：
```shell
func doSomething() error {
  if successCondition1 {
    // some success logic
    ... ...

    if successCondition2 {
      // some success logic
      ... ...

      return nil
    } else {
      // some error logic
      ... ...
      return err2
    }
  } else {
    // some error logic
    ... ...
  
    return err1
  }
}
```
伪代码段1
- 没有使用 else 分支，失败就立即返回
- 成功”逻辑始终“居左”并延续到函数结尾，没有被嵌入到 if 的布尔表达式为 true 的代码分支中
- 整个代码段布局扁平，没有深度的缩进
伪代码段2
- 整个代码段呈现为“锯齿状”，有深度缩进
- “成功”逻辑被嵌入到 if 的布尔表达式为 true 的代码分支中

## 重构

### 提高可读可维护
- 尝试将“正常逻辑”用函数提取出来
- 如果函数逻辑复杂，将深度缩进到 else 分支中的代码析出到一个函数中，再对原函数实施以上原则原则

### 提高性能
命中的概率高的if分支写前面

#### 原理（流水线技术和分支预测）

```text
1. 简单理解一条 CPU 指令的执行是由 取指令-指令译码-指令执行-结果回写组成的
2. 取指令就是if语句块的第一句，那么if如果不满足的话，就会执行JMP指令
3. 流水线中的取指令与指令译码其实是无用功
4. 在没有任何分支预测优化的情况下,取指令与指令译码其实是无用功
5. 现代计算机都有分支预测的优化，比如动态分支预测等技术
```
结论：
1. 概率更高的条件写到最上面,可以减少这种无用功
2. 现代计算机都有分支预测的优化，比如动态分支预测等技术，但是不管怎么说，把概率最大的放到最上面，还是很有必要的
