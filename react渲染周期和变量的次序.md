### 关于react中useState异步更新以后，触发页面重新渲染的逻辑

对比`useRef`、普通`let const`变量、useState值的变化次序

直接上代码实验
```tsx
export default function Home() {
  const [count, setCount] = useState(0);
  const countRef = useRef(count);
  let countRef2 ;
  console.log('outside', countRef.current, countRef2, count);
  const handleButtonClick = () => {
    console.log('inner-before', countRef.current, countRef2, count);

    setCount(count + 1);
    countRef.current = count;
    countRef2 = count;
    console.log('inner-after', countRef.current, countRef2, count);
  };

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={handleButtonClick}>Increment</button>
      <p>Previous count: {countRef.current}</p>
      <p>countRef2 count: {countRef2}</p>
    </div>
  );
}
```

页面第一次渲染
console输出：
outside 0 undefined 0

页面显示：
![a1.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9604949158994b59b995f01957869cc5~tplv-k3u1fbpfcp-watermark.image?)

第一次点击按钮以后

console输出：
inner-before 0 undefined 0
inner-after 0 0 0
outside 0 undefined 1

页面显示：
![a2.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9665adf296b14f64b32dc03ec9f34753~tplv-k3u1fbpfcp-watermark.image?)

 第二次点击按钮以后

console输出：
inner-before 0 undefined 1
inner-after 1 1 1
outside 1 undefined 2

页面显示：
![a3.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/054e67a2c51e404c921ccecd01d4f2b8~tplv-k3u1fbpfcp-watermark.image?)

 

#### 结论

由上面可以看到，

1. setCount更新count才触发页面渲染

2. 页面渲染时，先执行函数式组件里面的同步代码，再进行页面的渲染
   ```ts
   let countRef2 ;
   console.log('outside', countRef.current, countRef2, count);
   ```

3. 这里**重点**是`countRef.current`和普通变量`let countRef2`的比较。每次渲染，普通变量会被重新创建，之前的值会没掉。而useRef变量会被一直存放(闭包)，下次渲染可以拿到上一次渲染时的值。***这就是区别，本篇文章主要是想说这个。***

   