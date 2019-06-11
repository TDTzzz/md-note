# ng

**模板绑定是通过 property 和事件来工作的，而不是 attribute。**



**attribute 初始化 DOM property，然后它们的任务就完成了。property 的值可以改变；attribute 的值不能改变。**



当浏览器渲染 `<input type="text" value="Bob">` 时，它将创建相应 DOM 节点， 它的 `value` 这个 property 被*初始化为* “Bob”。

当用户在输入框中输入 “Sally” 时，DOM 元素的 `value` 这个 *property* 变成了 “Sally”。 但是该 HTML 的 `value` 这个 *attribute* 保持不变。如果你读取 input 元素的 attribute，就会发现确实没变： `input.getAttribute('value') // 返回 "Bob"`。

HTML 的 `value` 这个 attribute 指定了*初始*值；DOM 的 `value` 这个 property 是*当前*值。





