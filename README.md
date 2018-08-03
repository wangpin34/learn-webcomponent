# webcomponent 学习笔记
## 什么是 webcomponent
html 提供了很多各式各样的标签，用于展示内容(div, p, h1 - h7, span)，提供必要的交互动作(button, checkbox)。但 web app 是丰富多彩各不相同的，既有标签根本不能满足需要。富有创造力的 web 开发者们依靠 html/css/js 技术，发明了海量的美轮美奂的 web 组件，在此之上构建了无数兼具美感和功能性的应用。遗憾的是，一直以来，没有标准的 web 技术来定制化 web 标签，直到 webcomponent。 webcomponent 是专用于定制 web 元素的技术，包含[定制 html 元素](http://w3c.github.io/webcomponents/spec/custom/)，shadow dom，html imports 等核心概念。

## [定制 html 元素](http://w3c.github.io/webcomponents/spec/custom/)

### 纯定制化元素
定义类 FlagIcon，继承 [HTMLElement](https://html.spec.whatwg.org/multipage/dom.html#htmlelement)
```javascript
class FlagIcon extends HTMLElement {
  constructor() {
    super();
    this._countryCode = null;
  }

  static get observedAttributes() { return ["country"]; }

  attributeChangedCallback(name, oldValue, newValue) {
    // name will always be "country" due to observedAttributes
    this._countryCode = newValue;
    this._updateRendering();
  }
  connectedCallback() {
    this._updateRendering();
  }

  get country() {
    return this._countryCode;
  }
  set country(v) {
    this.setAttribute("country", v);
  }

  _updateRendering() {
    // Left as an exercise for the reader. But, you'll probably want to
    // check this.ownerDocument.defaultView to see if we've been
    // inserted into a document with a browsing context, and avoid
    // doing any work if not.
    this.innerHTML = '';
    var child = document.createElement('h1');
    child.innerText = this._countryCode;
    this.appendChild(child);
  }
}

```
注册 FlagIcon
``` javascript
customElements.define("flag-icon", FlagIcon);
```
添加元素 FlagIcon
``` html
<body>
  <flag-icon id="cd" country="nl"></flag-icon>
</body>
```

### 扩展原生 html 元素
扩展原生 button 元素，继承 [HTMLButtonElement](https://html.spec.whatwg.org/multipage/forms.html#htmlbuttonelement)
```javascript
class PlasticButton extends HTMLButtonElement {
  constructor() {
    super();

    this.addEventListener("click", () => {
      // Draw some fancy animation effects!
      alert('shake');
    });
  }
}
```
同样的，注册
```javascript
customElements.define("plastic-button", PlasticButton, { extends: "button" });
```
添加到 html 中
```html
 <button is="plastic-button">Click Me!</button>
```

## Shadow Dom
仅仅从浏览器加载的 DOM 树结果来看，自定义元素和web组件化技术（react，vuejs，angular）并无本质区别。后者所面临的困难，例如 css 互相影响，js非法修改组件内容，等等，前者也无法避免。那有没有一种技术，可以将每个元素或者组件所包含的 DOM 树完全隐藏起来，不为外界干扰呢？

有，这就是 Shadow Dom。

坦率的说，这个技术名词相当有水平：影子 Dom。所谓影子，它有形状，会变化，但没人能够改变影子（改变光源除外），除非本体。Shadow Dom 完全具备影子的首先：它的外观和功能来自于构造函数和外部输入。** 而且在运行期间，它的所有内容只受合法的输入值影响。**

```javascript
const header = document.createElement('header');
const shadowRoot = header.attachShadow({mode: 'open'});
shadowRoot.innerHTML = '<h1>Hello Shadow DOM</h1>'; // Could also use appendChild().
document.body.appendChild(header);
```

## HTML template

```html
  <div id="nameTag">Bob</div>
  <template id="nameTagTemplate">
    <style>
      .outer {
        border: 2px solid brown;
        border-radius: 1em;
        background: red;
        font-size: 20pt;
        width: 12em;
        height: 7em;
        text-align: center;
      }
      .boilerplate {
        color: white;
        font-family: sans-serif;
        padding: 0.5em;
      }
      .name {
        color: black;
        background: white;
        font-family: "Marker Felt", cursive;
        font-size: 45pt;
        padding-top: 0.2em;
      }
    </style>
    <div class="outer">
      <div class="boilerplate">
        Hi! My name is
      </div>
      <div class="name">
        Bob
      </div>
    </div>
  </template>
```

```javascript
  var shadow = document.querySelector('#nameTag').createShadowRoot();
  var template = document.querySelector('#nameTagTemplate');
  var clone = document.importNode(template.content, true);
  shadow.appendChild(clone);
```
## 更多资料
https://www.webcomponents.org/introduction
https://www.html5rocks.com/en/tutorials/webcomponents/customelements
https://www.html5rocks.com/en/tutorials/webcomponents/shadowdom/
[Can I use shadow dom](https://caniuse.com/#search=shadow)