Zarm 的 <ConfigProvider> 组件内部使用了 React.Children.only 方法，该方法只接受一个且仅一个子元素。

### 1. 可以用Fragment：<></>包裹这两个子元素，如下：

```jsx
<ConfigProvider primaryColor="#007fff" locale={zhCN}>
  <Routes>{/*...*/}</Routes>
  <NavBar showNav={true} />
</ConfigProvider>
```
改为：
```jsx
<ConfigProvider primaryColor="#007fff" locale={zhCN}>
  <>
    <Routes>{/*...*/}</Routes>
    <NavBar showNav={true} />
  </>
</ConfigProvider>
```
### 2. 也可以用普通div包裹。


