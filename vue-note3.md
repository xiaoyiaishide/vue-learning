### 事件处理
## 监听事件
可以用 v-on 指令监听 DOM 事件来触发一些 JavaScript 代码。
示例：
```
<div id="example-1">
  <button v-on:click="counter += 1">增加 1</button>
  <p>这个按钮被点击了 {{ counter }} 次。</p>
</div>
```

```
var example1 = new Vue({
  el: '#example-1',
  data: {
    counter: 0
  }
})
```

## 方法事件处理器
许多事件处理的逻辑都很复杂，所以直接把 JavaScript 代码写在 v-on 指令中是不可行的。因此 v-on 可以接收一个定义的方法来调用。
示例：

```
<div id="example-2">
  <!-- `greet` 是在下面定义的方法名 -->
  <button v-on:click="greet">Greet</button>
</div>
```

```
var example2 = new Vue({
  el: '#example-2',
  data: {
    name: 'Vue.js'
  },
  // 在 `methods` 对象中定义方法
  methods: {
    greet: function (event) {
      // `this` 在方法里指当前 Vue 实例
      alert('Hello ' + this.name + '!')
      // `event` 是原生 DOM 事件
      if (event) {
        alert(event.target.tagName)
      }
    }
  }
})
// 也可以用 JavaScript 直接调用方法
example2.greet() // => 'Hello Vue.js!'
```

## 内联处理器里的方法
除了直接绑定到一个方法，也可以用内联 JavaScript 语句：
```
<div id="example-3">
  <button v-on:click="say('hi')">Say hi</button>
  <button v-on:click="say('what')">Say what</button>
</div>
```

```
new Vue({
  el: '#example-3',
  methods: {
    say: function (message) {
      alert(message)
    }
  }
})
```
有时也需要在内联语句处理器中访问原生 DOM 事件。可以用特殊变量 $event 把它传入方法：
```
<button v-on:click="warn('Form cannot be submitted yet.', $event)">
  Submit
</button>
```

```
// ...
methods: {
  warn: function (message, event) {
    // 现在我们可以访问原生事件对象
    if (event) event.preventDefault()
    alert(message)
  }
}
```

## 事件修饰符

在事件处理程序中调用 event.preventDefault() 或 event.stopPropagation() 是非常常见的需求。尽管我们可以在 methods 中轻松实现这点，但更好的方式是：methods 只有纯粹的数据逻辑，而不是去处理 DOM 事件细节。
为了解决这个问题，Vue.js 为 v-on 提供了事件修饰符。通过由点 (.) 表示的指令后缀来调用修饰符。
.stop
.prevent
.capture
.self
.once

```
<!-- 阻止单击事件冒泡 -->
<a v-on:click.stop="doThis"></a>
<!-- 提交事件不再重载页面 -->
<form v-on:submit.prevent="onSubmit"></form>
<!-- 修饰符可以串联 -->
<a v-on:click.stop.prevent="doThat"></a>
<!-- 只有修饰符 -->
<form v-on:submit.prevent></form>
<!-- 添加事件侦听器时使用事件捕获模式 -->
<div v-on:click.capture="doThis">...</div>
<!-- 只当事件在该元素本身 (比如不是子元素) 触发时触发回调 -->
<div v-on:click.self="doThat">...</div>
```

使用修饰符时，顺序很重要；相应的代码会以同样的顺序产生。因此，用 @click.prevent.self 会阻止所有的点击，而 @click.self.prevent 只会阻止元素上的点击。

# 2.1.4 新增
```
<!-- 点击事件将只会触发一次 -->
<a v-on:click.once="doThis"></a>
```
不像其它只能对原生的 DOM 事件起作用的修饰符，.once 修饰符还能被用到自定义的组件事件上。如果你还没有阅读关于组件的文档，现在大可不必担心。

## 键值修饰符

在监听键盘事件时，我们经常需要监测常见的键值。Vue 允许为 v-on 在监听键盘事件时添加关键修饰符：
```
<!-- 只有在 keyCode 是 13 时调用 vm.submit() -->
<input v-on:keyup.13="submit">
```
记住所有的 keyCode 比较困难，所以 Vue 为最常用的按键提供了别名：
```
<!-- 同上 -->
<input v-on:keyup.enter="submit">
<!-- 缩写语法 -->
<input @keyup.enter="submit">
```
全部的按键别名：
.enter
.tab
.delete (捕获“删除”和“退格”键)
.esc
.space
.up
.down
.left
.right
可以通过全局 config.keyCodes 对象自定义键值修饰符别名：
```
// 可以使用 v-on:keyup.f1
Vue.config.keyCodes.f1 = 112
```

## 自动匹配按键修饰符
# 2.5.0 新增
你也可以通过将它们转换到 kebab-case 来直接使用由 KeyboardEvent.key 暴露的任意有效按键名作为修饰符：
```
<input @keyup.page-down="onPageDown">
```
在上面的例子中，处理函数仅在 $event.key === 'PageDown' 时被调用。
极少数的键 (.esc 以及所有的方向键) 在 IE9 中有不同的 key 值, 如果你想支持 IE9，它们的内置别名应该是首选。

## 系统修饰键
# 2.1.0 新增
可以用如下修饰符开启鼠标或键盘事件监听，使在按键按下时发生响应。
.ctrl
.alt
.shift
.meta
注意：在 Mac 系统键盘上，meta 对应命令键 (⌘)。在 Windows 系统键盘 meta 对应 windows 徽标键 (⊞)。在 Sun 操作系统键盘上，meta 对应实心宝石键 (◆)。在其他特定键盘上，尤其在 MIT 和 Lisp 键盘及其后续，比如 Knight 键盘，space-cadet 键盘，meta 被标记为“META”。在 Symbolics 键盘上，meta 被标记为“META”或者“Meta”。
例如：
```
<!-- Alt + C -->
<input @keyup.alt.67="clear">
<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Do something</div>
```
修饰键比正常的按键不同；修饰键和 keyup 事件一起用时，事件引发时必须按下正常的按键。换一种说法：如果要引发 keyup.ctrl，必须按下 ctrl 时释放其他的按键；单单释放 ctrl 不会引发事件。

## .exact 修饰符
# 2.5.0 新增
.exact 修饰符应与其他系统修饰符组合使用，以指示处理程序只在精确匹配该按键组合时触发。
```
  <!-- 即使 Alt 或 Shift 被一同按下时也会触发 -->
<button @click.ctrl="onClick">A</button>
 
  <!-- 只有在 Ctrl 被按下的时候触发 -->
<button @click.ctrl.exact="onCtrlClick">A</button>
```

## 鼠标按钮修饰符
# 2.1.0 新增
.left
.right
.middle
这些修饰符会限制处理程序监听特定的滑鼠按键。

## 为什么在 HTML 中监听事件?

你可能注意到这种事件监听的方式违背了关注点分离 (separation of concern) 传统理念。不必担心，因为所有的 Vue.js 事件处理方法和表达式都严格绑定在当前视图的 ViewModel 上，它不会导致任何维护上的困难。实际上，使用 v-on 有几个好处：
扫一眼 HTML 模板便能轻松定位在 JavaScript 代码里对应的方法。
因为你无须在 JavaScript 里手动绑定事件，你的 ViewModel 代码可以是非常纯粹的逻辑，和 DOM 完全解耦，更易于测试。
当一个 ViewModel 被销毁时，所有的事件处理器都会自动被删除。你无须担心如何自己清理它们。


### 表单输入绑定
## 基础用法
你可以用 v-model 指令在表单控件元素上创建双向数据绑定。它会根据控件类型自动选取正确的方法来更新元素。尽管有些神奇，但 v-model 本质上不过是语法糖，它负责监听用户的输入事件以更新数据，并特别处理一些极端的例子。
v-model 会忽略所有表单元素的 value、checked、selected 特性的初始值。因为它会选择 Vue 实例数据来作为具体的值。你应该通过 JavaScript 在组件的 data 选项中声明初始值。

对于要求 IME (如中文、日语、韩语等) (IME 意为“输入法”)的语言，你会发现 v-model 不会在 ime 输入中得到更新。如果你也想实现更新，请使用 input 事件。

# 文本
```
<input v-model="message" placeholder="edit me">
<p>Message is: {{ message }}</p>
```

# 多行文本
```
<span>Multiline message is:</span>
<p style="white-space: pre-line;">{{ message }}</p>
<br>
<textarea v-model="message" placeholder="add multiple lines"></textarea>
```