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
```javascript
const header = document.createElement('header');
const shadowRoot = header.attachShadow({mode: 'open'});
shadowRoot.innerHTML = '<h1>Hello Shadow DOM</h1>'; // Could also use appendChild().
document.body.appendChild(header);
```

## 更多资料
https://www.webcomponents.org/introduction 