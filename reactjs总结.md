## 组件中的传值

+ 父组件给子组件传值

  父组件通过属性的方式给子组件传递参数

  子组件通过props接受父组件传过来的参数

  ​

+ 子组件给父组件传值

  子组件要调用父组件传递过来的方法



## Fragment

使用Fragment可以替代最外层的div,

 return中一个div包裹的dom树就是一个jsx

```js
import React, { Component, Fragment } from 'react'

<Fragment>
  <div></div>
  <ul>{this.getTodoItem()}</ul>
</Fragment>
```



## 代码优化

```js
// 1 将事件绑定存储
this.inputToList = this.inputToList.bind(this)

// 代码优化2
getTodoItem() {
  return this.state.list.map((item, index) => {
    return (
      <ListItem del={this.handleDel} content={item} key={index} idx={index} />
    )
  })
}
// jsx中调用
<ul>{this.getTodoItem()}</ul>

// 代码优化3
  // this.setState 可以写成
  // 可以接收一个函数,这个函数需要一个返回值,return一个对象
  // 版本一
  this.setState( ()=>{
    return {
      list: [...this.state.list, this.state.inputDate],
      inputDate: ''
    }
  } )

  // 版本二
  // ES6中,若直接返回一个对象,可去掉return,用一对小括号包裹这个对象
  this.setState( () => ({
    list: [...this.state.list, this.state.inputDate],
    inputDate: ''
  }) )

  // 版本三
  // setState中的函数可以接收一个参数prevState, prevState等价于修改之前的数据,
  prevState <==> this.state
  this.setState( (prevState) => ({
    // list: [...this.state.list, this.state.inputDate],
    list: [...prevState.list, prevState.inputDate],
    inputDate: ''
  }) )
```



## 操作state中的数据

```js
// 删除
  handleDelete(index) {
    // 2 在删除数据的时候不要直接去操作state中的数据,
    // 先将数据拿出来,再操作(就是先拷贝一个副本)
     let list = this.state.list
     list.splice(index, 1)
     this.setState({
       list
     })
  }
```



## propTypes 与 defaultProps

```js
// propTypes
// 每个组件都有props参数,这个参数表示从父组件接收的一些属性
// 在接收参数是,我们改如何对接收的参数做校验,如何定义参数的默认值
// propTypes可以做接收属性的强校验
// 1 引入  脚手架中已自带了prop-types包
import PropTypes from 'prop-types';

// 2 对组件的类型进行强校验
// 意思就是我要对ListItem组件的属性做校验
ListItem.propTypes = {
  test: PropTypes.string.isRequired, // 必须传,且为string类型
  del: PropTypes.func.isRequired, // del接收的属性必须是一个函数,isRequired表示必须传
  idx: PropTypes.number, // idx类型必须是一个number类型
  content: PropTypes.arrayOf(PropTypes.number, PropTypes.string)
  // content类型必须是一个string类型,或者为number类型
}

// defaultProps
// defaultProps表示给属性添加默认值
// 当父组件没有传值得时候,添加默认值
ListItem.defaultProps = {
  test: 'hello world'
}
```



## render函数更新机制

> 当组件中的state或者props发生改变的时候,render函数就会重新执行(重)
>
> 当父组件的render函数被运行时,它的子组件的render都将被重新运行