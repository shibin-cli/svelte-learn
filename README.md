# Svelte学习笔记
[sveltejs](https://svelte.dev/)文档地址 https://svelte.dev，中文文档地址 https://www.sveltejs.cn/
## 开始
创建一个应用，文档参考 https://www.sveltejs.cn/blog/svelte-for-new-developers
``` bash
npx degit sveltejs/template svelte-app
cd svelte-app
```
上面如果创建失败，可以将[sveltejs](https://svelte.dev/)的模板通过git下载到本地
``` bash
git clone https://github./comsveltejs/template.git
cd template
npm install
```
如果要使用typescript，执行下面
``` bash
# 如果要使用typescript，执行下面
node scripts/setupTypeScript.js
```
## 简介
### 添加数据
声明一个变量，在页面中使用花括号就可以将变量的内容显示到页面上(跟React很像)
``` svelte
<script lang="ts">
	let name: string = 'world'
    setTimeout(() => {
        name = 'shibin'
	}, 2000)
</script>
<main>
    <h1>Hello {name}!</h1>
</main>
```
在页面上显示的html
``` html
<h1>Hello world!</h1>
```
### 添加样式
在组件中添加一个`<style>`标签，`<style>`标签中的样式只在改组件内有效（类似vue中的`<style>`标签中加上scoped）
``` svelte
<script lang="ts">
	let name: string = 'world'
</script>

<main>
	<h1>Hello {name}!</h1>
	<p>
		Visit the <a href="https://svelte.dev/tutorial">Svelte tutorial</a> to learn
		how to build Svelte apps.
	</p>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
```
### 嵌套组件
在 `<script> `中导入组件就可以使用了
``` svelte
<script lang="ts">
	import Hello from './Hello.svelte'
</script>

<main>
	<Hello/>
</main>
```
### html标签
通常，字符串以纯文本形式插入，如果要插入html代码片段，可以使用`{@html ...}`实现
``` svelte
<p>{@html string}</p>
```
### 创建一个应用
Svelte提供了Rollup和Webpack的插件，可以根据需要进行配置
* [rollup-plugin-svelte](https://github.com/sveltejs/rollup-plugin-svelte)
* [svelte-loader](https://github.com/sveltejs/svelte-loader)
[其他插件](https://github.com/sveltejs/integrations#bundler-plugins)
也可以直接使用上面（开始）下载的模板

项目配置好，编译器会将每个组件转化为常规的JavaScript类，接下来只需导入它并用 new 实例化即可
``` ts
import App from './App.svelte'

const app = new App({
	target: document.body,
	props: {
		name: 'world'
	}
})

export default app
```
## 赋值
### Reactivity
Svelte能够让 DOM 与你的应用程序状态保持同步，你只需要改变数据，Svelte 会自动更新 DOM
``` svelte
<script lang="ts">
	import Hello from './Hello.svelte'
	let count: number = 0
	const handleClick = () => {
		count += 1
	}
</script>
<button on:click={handleClick}>Clicked {count} times</button>
```
### 声明
当组件的某个部分需要其他部分计算得出时（例如Vue中的计算属性），Svelte提供了reactive declarations
``` svelte
<script lang="ts">
    let count = 0
    $: doubled = count * 2
</script>
```
在页面中使用`{count * 2}`也能达到效果，但当需要引用多次时，使用这种声明会更好
### 语句
语句也可以通过计算

下面代码，当count值发生改变时，就会执行语句
``` svelte
<script lang="ts">
    let count = 0
    $: console.log(`the count is ${count}`);
</script>
```
将一组语句组合成一个代码块
``` svelte
<script lang="ts">
    let count = 0
    $: {
	console.log(`the count is ${count}`)
	alert(`I SAID THE COUNT IS ${count}`)
    }
</script>
```
甚至可以将 $: 放在 if 代码块前面。下面代码当count>=10时，就是执行if里的代码块
``` svelte
<script lang="ts">
    let count = 0
    $: if (count >= 10) {
	  alert(`count is dangerously high!`)
	  count = 9
    }
</script>
```
### 更新数组和对象
Svelte的数据更新是由赋值语句触发的，所以使用数组的`push`、`slice`、`pop`等不会触发自动更新。

下面代码不会更新
``` svelte
<script lang="ts">
	let arr:number[] = [1,2,3]

	function addNumber(){
		arr.push(arr.length + 1)
	}
	
</script>

<main>
	{arr}
	<button on:click={addNumber}>addNumber</button>
</main>
```
解决的办法是重新赋值
``` svelte
<script lang="ts">
	import Hello from './Hello.svelte'
	let arr:number[] = [1,2,3]

	function addNumber(){
		arr.push(arr.length + 1)
        arr = arr
	}
</script>
```
还有一种更惯用的方法
``` js  
function addNumber() {
	arr = [...arr, arr.length + 1]
}
```
赋值给数组和对象的 属性（properties）与对值本身进行赋值的方式相同。
``` js
function addNumber() {
    arr[arr.length] = arr.length + 1
}
```
一个简单的经验法则是：**被更新的变量的名称必须出现在赋值语句的左侧**
``` js
const foo = obj.foo;
// 不会触发obj.foo.bar 的引用
foo.bar = 'baz';

// 会触发obj.foo.bar 的引用
// obj.foo.bar = 'baz';
```
就不会更新对 obj.foo.bar 的引用，除非使用 obj = obj 方式。
## Props
props用于父组件向子组件中传递数据
``` html
<Hello name="world"/>
```
子组件可以通过`export `关键字来获取父组件传递的数据
``` svelte
<script lang="ts">
    export let name: string
</script>

<h1>Hello {name}!</h1>
```
为props设置默认值
``` ts
export let name: string = 'Shibin'
```
如果你的组件含有有一个对象属性，可以利用`...`对象解构语法将数据传递给子组件
``` html
<Hello {...obj}/>
```
## 逻辑
### 条件渲染
if
``` html
{#if user.loggedIn}
	<button on:click={toggle}>
		Log out
	</button>
{/if}

{#if !user.loggedIn}
	<button on:click={toggle}>
		Log in
	</button>
{/if}
```
else
``` html
{#if user.loggedIn}
	<button on:click={toggle}>
		Log out
	</button>
{:else}
	<button on:click={toggle}>
		Log in
	</button>
{/if}
```
else if
``` html
{#if x > 10}
	<p>{x} is greater than 10</p>
{:else if 5 > x}
	<p>{x} is less than 5</p>
{:else}
	<p>{x} is between 5 and 10</p>
{/if}
```
### 列表渲染
``` svelte
<script lang="ts">
	let arr: number[] = [1, 2, 3]
</script>

<main>
	<ul>
		{#each arr as number}
			<li>{number}</li>
		{/each}
	</ul>
</main>
```
你也可以将index 作为第二个参数(key)，类似于：
``` html
{#each arr as number, index}
    <li>{index} {number}</li>
{/each}
```
一般来说，当你修改each 块中的值时，它将会在 尾端 进行添加或删除条目，并更新所有变化， 这可能不是你想要的效果。

为此，我们为 each 块指定一个唯一标识符，作为 key 值：

``` html
{#each things as thing (thing.id)}
	<Thing current={thing.color}/>
{/each}
```
(thing.id) 告诉 Svelte 什么地方需要改变。
### Await
使用await在标签中处理异步数据
``` html
{#await promise}
	<p>...waiting</p>
{:then number}
	<p>The number is {number}</p>
{:catch error}
	<p style="color: red">{error.message}</p>
{/await}
```
如果在请求完成之前不想程序执行任何操作，也可以忽略第一个块。
``` svelte
{#await promise then value}
	<p>the value is {value}</p>
{/await}
```
## 事件
在元素中使用on: directive可以为元素添加所任何事件
``` html
<div on:mousemove={handleMousemove}>
	The mouse position is {m.x} x {m.y}
</div>
```
也可以声明内联事件处理函数
``` html
<div on:mousemove="{e => m = { x: e.clientX, y: e.clientY }}">
	The mouse position is {m.x} x {m.y}
</div>
```
### 事件修饰符
在Svelte中，DOM 事件处理程序具有额外的 修饰符
``` html
<button on:click|once={handleClick}>
	Click me
</button>
```
Svelte中事件修饰符有
* preventDefault 调用event.preventDefault() ，在运行处理程序之前调用，组织浏览器的默认行为
* stopPropagation  调用 event.stopPropagation(), 防止事件冒泡
* passive 优化了对 touch/wheel 事件的滚动表现(Svelte 会在合适的地方自动添加滚动条)。
* capture 在 capture 阶段而不是bubbling 阶段触发事件处理程序
* once 运行一次事件处理程序后将其删除
* self 仅当 event.target 是其本身时才执行

组合使用：`on:click|once|capture={...}`
### 组件事件
组件可以向父组件发送自定义事件
``` svelte
<script lang="ts">
    import {createEventDispatcher} from 'svelte'
    export let name: string = 'wd';
    const dispatch = createEventDispatcher()
    function sayHello(){
        dispatch('message',{
            text: 'hello'
        })
    }
</script>

<h1>Hello {name}!</h1>
<button on:click={sayHello}>Say hello</button>
```
父组件接收自定义事件
``` svelte
<script>
	function handleMessage(e) {
		alert(e.detail.text)
	}
</script>
<Hello on:message={handleMessage}/>
```
**注意**， 组件事件不会冒泡，如果需要多层传递,则需要转发(自定义事件一层层传递)

事件转发也可以应用到DOM事件。
``` html
<button>
	Click me
</button>
```
``` html
<FancyButton on:click={handleClick}/>
```
## 绑定
### 表单绑定
``` svelte
<script lang="ts">
	let name: string = 'shibin'
</script>
<div>
	<input bind:value={name}>
	{name}
</div>
```
#### number
Svelte中可以拿到表单中的数值，就是number类型的
``` svelte
<script>
	let a = 1;
	let b = 2;
</script>

<label>
	<input type=number value={a} min=0 max=10>
	<input type=range value={a} min=0 max=10>
</label>

<label>
	<input type=number value={b} min=0 max=10>
	<input type=range value={b} min=0 max=10>
</label>

<p>{a} + {b} = {a + b}</p>
```