### 1. 你的第一个组件

- 导出组件export default，可定义时导出，也可最后导出。

- 组件的名称必须以大写字母开头，属性驼峰式命名。

- 标签和 `return` 关键字不在同一行，则必须把它包裹在一对括号中；

- 组件中可以渲染其他组件，但不能在组件中定义其他组件。

### 2. 组件的导入与导出

#### 默认导出与具名导出：

- 一个文件里**有且仅有**一个默认导出，可以有多个具名导出。

- 默认导入，可以在 `import` 语句后面进行任意命名；具名导入，导入和导出的名字必须一致。

  | 语法 | 导出语句                              | 导入语句                                |
  | ---- | ------------------------------------- | --------------------------------------- |
  | 默认 | `export default function Button() {}` | `import Button from './Button.js';`     |
  | 具名 | `export function Button() {}`         | `import { Button } from './Button.js';` |

### 3. 使用 JSX 书写标签语言

- 只能返回一个根元素，如果想要在一个组件中包含多个元素，需要用一个父标签（`<div></div>或<></>`）把它们包裹起来。

  - 为什么多个 JSX 标签需要被一个父元素包裹？
    - JSX底层转化成了JS对象，不能在一个函数中返回多个对象。

  > ⚠️注意，`<></>标签不能支持指定key，需要使用<Fragment></Fragment>代替。`

### 4. 在 JSX 中通过大括号使用 JavaScript

- JSX 中，在哪使用大括号？

  - JSX 标签内的文本，此时name应作为一个变量嵌入`<h1>{name}'s To Do List</h1>`；
  - 紧跟在 `=` 符号后的属性，`src={avatar}`，而`src="{avatar}"` 只会传一个字符串 `{avatar}`。

- JSX中，何时使用双大括号？

  - CSS样式和传递对象，双大括号即包裹在大括号中的一个对象。

- 如何在JSX中编写表达式？

  ```jsx
  // ❌错误方式
  src="{baseUrl}{person.imageId}{person.imageSize}.jpg"
  
  // ✅正确方式
  src={baseUrl + person.imageId + person.imageSize + '.jpg'}
  ```

### 5. 将 Props 传递给组件

- 什么是props？

  - props是传递给JSX标签的信息，比如className、src、alt、height、width是传递给`<img>`标签的props。

- 父组件如何将props传递给子组件？子组件如何接受父组件传递的props？

  ```jsx
  // 父组件Profile中将props传递给子组件Avatar
  export default function Profile() {
    return (
      <Avatar
        person={{ name: 'Lin Lanying', imageId: '1bX5QH6' }}
        size={100}
      />
    );
  }
  
  // 子组件接受从父组件传递的props
  function Avatar({ person, size }) {
    // 在这里 person 和 size 是可访问的
  }
  ```

  - ⚠️注意，props之间没有`,`隔开，即person与size之间没有`,`号。

- prop默认值何时生效，何时不生效？

  - 以上述size prop为例，当Profile父组件中缺少size props、size = {undefined}、size = {null}时，子组件中size prop默认值生效；当size = {0}时，默认值不生效。
    - ⚠️注意，官网说size = {null}时也不生效，但是实践表明此时是生效的。

- JSX中展开语法使用：

  ```jsx
  function Profile({ person, size, isSepia, thickBorder }) {
    return (
      <div className="card">
        <Avatar
          person={person}
          size={size}
          isSepia={isSepia}
          thickBorder={thickBorder}
        />
      </div>
    );
  }
  
  // 替换为展开语法写法
  function Profile(props) {
    return (
      <div className="card">
        <Avatar {...props} />
      </div>
    );
  }
  ```

  - ⚠️注意，官方提醒警惕使用展开语法。如果在所有组件中都使用，会出问题，具体什么问题，留个疑问❓

- 父组件的children prop可以接受嵌套在它之内的内容：

  ```jsx
  import Avatar from './Avatar.js';
  
  function Card({ children }) {
    return (
      <div className="card">
        {children}
      </div>
    );
  }
  
  export default function Profile() {
    return (
      <Card>
        <Avatar
          size={100}
          person={{ 
            name: 'Katsuko Saruhashi',
            imageId: 'YfeOqp2'
          }}
        />
      </Card>
    );
  }
  ```

- 如何理解props 不是静态的？而又是props 是不可变的？

  - props 不是静态的：对应的是react是动态渲染的，即上层组件更新数据，将新的props传递给子组件。

  - props 是不可变的：即不能在子组件中直接修改props，如果需要自己修改某个值，可以通过state。

    ```jsx
    function Clock({ color, time }) {
      // ❌ 错误示例：
      color = 'blue'; // 不要这样做！
      return <h1 style={{ color }}>{time}</h1>;
    }
    ```

> 练习官网对应挑战。

### 6. 条件渲染

- 三目运算符
- 与运算符&&，在JSX中，比如a && b：
  - 若a是false、undefined、null，什么都不渲染；
  - 若a是数字，渲染该数字；
  - 若a真值为true，包括字符串等，渲染b。
  - ⚠️注意，不要将数字放在&&左侧。比如该错误实例`{messageCount && <p>New messages</p>} `，我们期待真值为false时，不渲染结果，真值为true时，渲染右侧结果。但是若左侧为数字，比如0，真值为false，但是渲染的是0，而不是我们期待的不渲染任何结果。

> 第二个挑战、第三个挑战。

### 7. 渲染列表

- 使用filter过滤数据与使用map遍历数据：

  ```jsx
  import { people } from './data.js';
  import { getImageUrl } from './utils.js';
  
  export default function List() {
    const chemists = people.filter(person =>
      person.profession === '化学家'
    );
    const listItems = chemists.map(person =>
      <li>
        <img
          src={getImageUrl(person)}
          alt={person.name}
        />
        <p>
          <b>{person.name}:</b>
          {' ' + person.profession + ' '}
          因{person.accomplishment}而闻名世界
        </p>
      </li>
    );
    return <ul>{listItems}</ul>;
  }
  ```

  - ⚠️注意，箭头函数可以隐士返回`=>`后面的表达式，因此可以省略return语句；但是若`=>`后面加了`{}`，那么必须使用`return`来返回值。
  - ⚠️注意，上述代码会出现key警告，我们为li标签添加key值。

- 为什么需要key？

  - 若使用数组id代替key，比如删除第二项，原有第三项及之后索引-1，第3项-倒数第二项会依次复用前一位的DOM节点，最后一项DOM节点会删除。
  - 若使用动态随机key，由于每次都产生新的key，react执行diff算法会认为所有列表项是新的，进而会销毁原有DOM，创建新的DOM，造成性能问题。
  - 需要在此基础山继续扩展一下diff算法❓
    - 如果 key 相同，diff算法认为是同一个节点，进行复用；如果 key 不同，则认为是不同节点，销毁旧节点、创建新节点。

> 反复体会四个挑战。

### 8. 保持组件纯粹

- 什么是纯函数？什么是副作用？

  - 纯函数：函数执行不会更改外部变量；函数多次执行，输出结果一致。
  - 副作用：函数执行过程中，对外部的影响，比如更改外部变量、修改DOM、发起网络请求等。

- 如何避免非纯函数？

  - （不作修改）组件只返回JSX，不在渲染前更改已经存在的变量，使用prop来传入值，值更改由上层组件完成；
  - （修改）直接将变量定义在组件内部，即事件处理程序中，然后更改其值；
  - （修改）最后手段，将副作用放在useEffect中。useEffect会在渲染完成后执行。
    - 注意，不要滥用useEffect，它会使数据流混乱、调试困难、性能下降。

- 如何检测是否是纯函数？

  - 使用严格模式，即`<React.strictMode>`包裹根组件。
    - 原理是严格模式下，会重复调用两次函数组件。

- 挑战一：

  ```jsx
  // ❌错误代码
  export default function Clock({ time }) {
    const hours = time.getHours();
    if (hours >= 0 && hours <= 6) {
      document.getElementById('time').className = 'night';
    } else {
      document.getElementById('time').className = 'day';
    }
    return (
      <h1 id="time">
        {time.toLocaleTimeString()}
      </h1>
    );
  }
  ```

  - 错误原因：

    - 组件执行时调用`document.getElementById('time')`操作真实DOM，此时React还没有将`<h1 id = 'time'>`渲染到页面上，所以通过id获取DOM节点时会出现null错误；
    - React通过操作虚拟DOM来管理页面状态，这里直接操作真实DOM，会导致虚拟状态和真实状态不同步；
    - 函数执行时修改了外部DOM节点，不是纯函数。

  - 修正：

    ```jsx
    // ✅正确代码
    export default function Clock({ time }) {
      const hours = time.getHours();
      const className = hours >= 0 && hours <= 6 ? 'night' : 'day';
      return (
        <h1 className={className}>
          {time.toLocaleTimeString()}
        </h1>
      );
    }
    ```

> 体会剩余挑战。

### 9. 将 UI 视为树

可以通过将组件间、引入模块之间的关系通过树图来显示展示。
