# React学习笔记

## JSX
1. JSX是表达式;
2. 写JSX的属性时要注意，如果属性值是字符串则用双引号`""`，如果属性值是表达式则用花括号`{}`，两者不能同时出现;

    ````jsx
    const element = <img src={user.avatarUrl}></img>;
    ````

3. React DOM 使用 `camelCase` 格式的属性，如 class 要写成 className, tabindex 要写成 tabIndex;
4. React DOM 会在渲染之前将嵌入到 JSX 中表达式的值进行转义以防止 XSS 攻击；

## Rendering Elements

React 中的 element 是 components 的组成部分，一个 element 更像是电影中的一帧，element 一旦创建则不可以修改它的属性及其子元素。

在 React 中有一种 `Only Updates What's Necessary` 的机制，在每一次渲染 UI 的过程中，React 只会更新需要被更新的子节点，而不是把整个 DOM 根节点替换。

## Components and Props

React 的 Components 分为 function component 和 class component。function component 写法更加简洁，class component 则支持更多特性。

### function component
````jsx
function Welcome(props) {
    return <h1>Hello, {props.name}</h1>;
}
````

### class component
````jsx
class Welcome extends React.Component {
    render() {
        return <h1>Hello, {this.props.name}</h1>;
    }
}
````

1. React 默认把小写字母开头的 component 当作 DOM 标签，如 `<div />`，把大写字母开头的 component 当作 React 组件，如上面写到的 `<Welcome />` 组件。

## Props are Read-Only

React is pretty flexible but is has a single strict rule:

**All React components must act like pure functions with respect to their props.**

## State and Lifecycle


