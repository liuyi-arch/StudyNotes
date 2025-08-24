# React

## 1. react状态管理

- State（组件内部状态）：状态改变会触发组件重新渲染

  ```js
  function Counter() {
    const [count, setCount] = React.useState(0);
  
    return (
      <div>
        <p>当前计数: {count}</p>
        <button onClick={() => setCount(count + 1)}>增加</button>
      </div>
    );
  }
  // setCount(5); // count 会变成 5
  // setCount(prev => prev + 1); // 在前一个值基础上加 1
  ```

  - useState(0)中0为`count` 初始值；

  - `setCount` 是更新 `count` 的函数；

    ```js
    setCount(count + 1);
    setCount(5); // count 会变成 5
    setCount(prev => prev + 1); // 在前一个值基础上加 1
    ```

    

- Props（父组件传递的数据）：子组件不能直接修改 props

  ```js
  function Child({ message }) {
    return <p>{message}</p>;
  }
  
  function Parent() {
    return <Child message="来自父组件的数据" />;
  }
  ```

- 子传父（回调函数）：

  ```js
  function Child({ onSend }) {
    return <button onClick={() => onSend("子组件数据")}>发送</button>;
  }
  
  function Parent() {
    const handleSend = (msg) => console.log("收到：", msg);
    return <Child onSend={handleSend} />;
  }
  ```

- 兄弟组件通信：通过父组件中转；

- 第三方库Redux / Zustand / MobX / Recoil / Jotai：全局状态管理；

## 2. Hooks

- useState：
- useEffect：
- useContext：跨组件层级传递数据
- useMemo / useCallback：性能优化：避免不必要的计算或函数重新创建
