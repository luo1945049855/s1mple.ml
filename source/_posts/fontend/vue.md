---
title:  "Vue"
date:   2020-06-14 
categories: Frontend
---

## Template

## Class Bindings

```js

<div id="inlineClassBind" v-bind:class="{ active: isActive }">Class Bind</div>
<script>
    var active = new Vue({
        el: "#inlineClassBind",
        data: {
            isActive: false
        }
    })
</script>

It will render:

<div class="active"></div>

```

```js
<div
  class="static"
  v-bind:class="{ active:isActive, 'text-danger': hasError }"
></div>

data: {
    isActive: true,
    hasError: false
}
It will render:
<div class="static active"></div>

data: {
    isActive: true,
    hasError: true
}
It will render:
<div class="static active text-danger"></div>

```

## Style Bindings

```js
<div id="style-binding" v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }">style bindings</div>
<script>
    var titleStyleBinding = new Vue({
        el: "#title-style-binding",
        data: {
            activeColor: 'red',
            fontSize: 30
        }
    })
</script>

<!--object syntax-->
<div id="style-binding-object" v-bind:style="styleObject"></div>
<script>
    var styleBindingObject = new Vue({
        el: "#style-binding-object",
        data: {
            styleObject: {
                color: 'red',
                fontSize: '13px'
            }
        }
    })
</script>

/*<!--multiple values-->*/
<div v-bind:style="{ display: ['-webkit-box', '-ms-flexbox', 'flex']}"></div>

/*<!--array syntax-->*/
<div v-bind:style="[baseStyles, overridingStyles]"></div>

```

## Conditional Rendering

### v-if

```js
<h1 v-if="awesome">Vue is awesome!</h1>
<h1 v-else>Oh no it's not!</h1>
```

### v-if on `<template>`

```js
<template v-if="ok">
    <h1>Title</h1>
    <p>Paragraph 1</p>
    <p>Paragraph 2</p>
</template>
```

### v-if/v-else-if/v-else

```js
<div v-if="type === A"></div>
<div v-else-if="type === B"></div>
<div v-else="type === C"></div>
```

### controlling reusable elements with key

```js
<template v-if="loginType === 'username'">
    <lable>Username</label>
    <input placeholder="Enter your username"></input>
</template>
<template v-else>
    <label>Email</label>
    <intput placeholder="Enter your email address"></input>
</template>

<!--in this condition placeholder is reused.-->
<!--These two elements are completely separate when two unique `key` are assigned.-->

<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username" key="username-input">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address" key="email-input">
</template>
```

### v-if VS v-show

```js
<h1 v-show="ok">Hello!</h1>
```

The difference is that an element with `v-show` will always be rendered and remain in the DOM; `v-show only` toggles the `display` CSS property of the element.

### v-if with v-for

using `v-if` and `v-for` together is not recommended.



## Event Handling

### Listening To Events

`v-on` listen to DOM events.

### Method Event Handlers

### Event Modifier

* .stop
* .prevent

<!-- use capture mode when adding the event listener -->
<!-- i.e. an event targeting an inner element is handled here before being handled by that element -->

* .capture

<!-- only trigger handler if event.target is the element itself -->
<!-- i.e. not from a child element -->

* .self

<!-- the click event will be triggered at most once -->

* .once

<!-- the scroll event's default behavior (scrolling) will happen -->
<!-- immediately, instead of waiting for `onScroll` to complete  -->
<!-- in case it contains `event.preventDefault()`                -->

```js
<div v-on:scroll.passive="onScroll">...</div>
```

* .passive

`Order matters` when using modifiers because the relevant code is generated in the same order.
Therefore using `v-on:click.prevent.self` will prevent `all clicks`
while `v-on:click.self.prevent` will only prevent clicks `on the element itself`.

### Key Modifiers

```js
<!-- only call `vm.submit()` when the `key` is `Enter` -->
<input v-on:keyup.enter="submit"></input>
```

### System Modifier Keys

* .ctrl
* .alt
* .shift
* .meta <!--Windows key/Command key-->

for example:

```js
<!--alt+c-->
<intput @keyup.alt.67="clear">

<!--Ctrl + Click-->
<div @click.ctrl="doSomething">Do something</div>
```

### .exact Modifier

```js
<button @click.ctrl="onClick">A</button>
<!--this will fire even if alt or shift is also pressed.-->
<!--this will only fire when Ctrl and no other keys are pressed.-->
<button @click.ctrl.exact="onCtrlClick">B</button>
```

### Mouse Button Modifier

* .left
* .right
* .middle



## Form Input Binding

`v-model` can create two-way bindings on `form input`, `text area`, `select elements`.

`v-model` will ignore the initial `value`, `checked` or `selected` attributes found on any form elements.
You should declare the initial value on the JavaScript side, inside the `data` option of your component.

### Binded `Properties` and `Events`

`v-model` internally uses different properties and emits different events for different input elements.

* `text` and `textarea` elements use `value` property and `input` event.
* `checkbox` and `radiobuttons` use `checked` property and `change` event.
* `select fields` use `value` as a prop and `change` as an event.

### Text

### Multiline Text

### Checkbox

### Radio

### Select

### Modifiers

* .lazy

`v-model` syncs the input with the data after each `input` event(with the exception of IME composition).
You can add the `lazy` modifiers to instead sync after `change` events.

* .number
* .trim

## List Rendering

<!--item in/of items-->
<!--(item, index) in/of items-->
<!--value in object-->
<!--{{ name }}: {{ value }}-->
<!--{{ index }}. {{ name }}: {{ value }} -->

```js
<ul id="example">
    <li v-for="item in items">
        {{ item.message }}
    </li>
</ul>
<ul id="example-1">
    <li v-for="(item, index) in items">
        {{ parentMessage }} - {{ index }} - {{ item.message }}
    </li>
</ul>

<ul id="v-for-object">
    <li v-for="value in object">
        {{ value }}
    </li>
</ul>

<!--value, name-->
<ul id="v-for-object-name-value">
    <li v-for="(value, name) in object">
        {{ name }}: {{ value }}
    </li>
</ul>

<!--value, name, index-->
<div v-for="(value, name, index) in object">
  {{ index }}. {{ name }}: {{ value }}
</div>

<script>
    var example = new Vue({
        el:"#example",
        data: {
            items: [
                { message: 'Foo' },
                { message: 'Bar' }
            ]
        }
    })
    var example1 = new Vue({
        el:'#example-1',
        data: {
            parentMessage: 'Parent',
            items: [
                { message: 'Foo' },
                { message: 'Bar' }
            ]
        }
    })
    var exampleVForObject = new Vue({
        el: "#v-for-object",
        data: {
            title: 'How to do lists in Vue',
            author: 'Jane Doe',
            publishedAt: '2016-04-10'
        }
    })
</script>
```

### Array Change Detection

Vue wraps an observed array's mutation method so they will also trigger view updates, Include:

* push()
* pop()
* shift()       <!--remove first element from array and return that removed element-->
* unshift()     <!--add one or more elements to the beginning of an array and return the new lengh of the array-->
* splice()      <!--change the contents of an array by removing or replacing existing elements and/or adding new elements-->
* sort()
* reverse()

### Replacing an Array

example1.items = example1.item.filter(function (item) {
    return item.message.match(/Foo/)
})

### Caveats

Vue can't detect the following changes to an array.

1. set and item with the index, e.g. vm.item[indexOfItem] = newValue
2. modify the length of the array, e.g. vm.item.length = newLength
3. Vue can't detect property addition or deletion, e.g.

```js
var vm = new Vue({
    data: {
        a: 1
    }
})
vm.b = 2
```

solutions:

1. caveat 1
Vue.Set(vm.items, indexOfItem, newValue)
vm.$set(vm.items, indexOfItem, newValue) <!--alias for Vue.Set-->
vm.items.splice(indexOfItem, 1, newValue)

2. caveat 2
vm.items.splice(newLength)

3. caveat 3

Vue does not allow dynamically adding new `root-level` reactive properties to an already created instance.
However, it't possible to add reactive properties to a nested object using the `Vue.set(object, propertyName, value)`. For example:

```js
var vm = new Vue({
    data: {
        userProfile: {
            name: 'Anika'
        }
    }
})
Vue.set(vm.userProfile, 'age', 27)
vm.$set(vm.userProfile, 'age', 27)  <!--alias for Vue.set-->
```

Sometimes you may want to assign a number of new properties to an existing object, for example using Object.assign() or _.extend().
In such cases, you should create a fresh object with properties from both objects. So instead of:

```js
Object.assign(vm.userProfile, {
    age: 27,
    favoriteColor: 'Vue Green'
})
```

you would add new, reactive properties with:

```js
vm.userProfile = Object.assign({}, vm.userProfile, {
    age: 27,
    favoriteColor: 'Vue Green'
})
```

### Display Filtered/Sorted Results

solution 1:

```js
<li v-for="n in evenNumbers">{{ n }}</li>

var evenNumbers = new Vue({
    data: {
        numbers: [1, 2, 3, 4, 5]
    }
    computed: {
        evenNumbers: function() {
            return this.numbers.filter(function (number) {
                return number % 2 === 0
            })
        }
    }
})
```

solution 2(where computed properties are not feasible):

```js
<li v-for="n in even(numberse">{{ n }}</li>
methods: {
    even : function (numbers) {
        return numbers.filter(function (number) {
            return number % 2 === 0
        })
    }
}
```

### v-for with a Range

```js
<div>
    <span v-for="n in 10">{{ n }}</span>
</div>
```

### v-for on a `<template>`

```js
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```

### v-for and v-if

It's not recommended to use v-for and v-if together.
When they exist on the same node, v-for has a higher priority than v-if.

### v-for with a Component

a `key` is now required.

```js
<my-component v-for="item in items" :key="item.id"
    v-for="(item, index) in items"
    v-bind:item="item"
    v-bind:index="index"
    v-bind:key="item.id"
></my-component>
```
