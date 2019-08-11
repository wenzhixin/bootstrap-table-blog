---
title: "Formatter Use Vue Component"
date: 2019-08-10T20:32:40+08:00
categories: [Vue]
---

Starting with v1.15.2, the Bootstrap Table adds support for the Bootstrap Table Vue component.

We can use the two column options `formatter` and `events` to implement custom display and event listen for columns:

```html
<template>
  <BootstrapTable
    :columns="columns"
    :data="data"
    :options="options"
  />
</template>

<script>
import BootstrapTable from 'bootstrap-table/dist/bootstrap-table-vue.esm.js'

export default {
  components: {
    BootstrapTable
  },
  data () {
    return {
      columns: [
        {
          title: 'Item ID',
          field: 'id'
        },
        {
          field: 'name',
          title: 'Item Name'
        },
        {
          field: 'price',
          title: 'Item Price'
        },
        {
          field: 'actions',
          title: 'Actions',
          formatter: () => {
            return '<button class="btn btn-secondary">Click</button>'
          },
          events: {
            'click .btn': (e, value, row) => {
              this.clickRow(row)
            }
          }
        }
      ],
      data: [
        {
          id: 1,
          name: 'Item 1',
          price: '$1'
        }
      ],
      options: {
        search: true,
        showColumns: true
      }
    }
  },
  methods: {
    clickRow (row) {
      alert(JSON.stringify(row))
    }
  }
}
</script>
```

Here's an online example: https://live.bootstrap-table.com/code/wenzhixin/440

As you can see, the `clickRow` method is called when we click the button, but we are using the event binding method of `jQuery`.

With `vue`, you might wonder if I can bind using the event of `vue`?

Such as:

```js
formatter: (value, row) => {
  return '<button class="btn btn-secondary" @click="clickRow(row)">Click</button>'
}
```

The answer is no, because the event of `vue` is not handled here.

In addition, we may use the UI component of `vue`, for example [BootstrapVue
] (https://bootstrap-vue.js.org/), then you might wonder, can the `formatter` here use the `vue` component?

Such as:

```js
formatter: (value, row) => {
  return '<b-button @click="clickRow(row)">Click</b-button>'
}
```

Of course, this is also not supported. What should we do?

For these two questions, I conducted in-depth research and experimentation. Fortunately, a viable solution was found.

The technical principle is mainly to save the `vue` component to a custom variable, and then return a simple `div` containing only `class`. After the table is rendered, we will convert it to the corresponding `vue` Component.

We have added a method:

```js
vueFormatter (obj) {
  const key = `_vue_formatter_${this.vueFormatters.length}`
  this.vueFormatters.push({
    el: `.${key}`,
    name: key,
    ...obj
  })
  return `<div class="${key}"/>`
}
```

You can see that we use the `vueFormatters` variable to save the `vue` component information for all columns.

We know that the rendered event of the table is `onPostBody`, which we listen to and render the saved `vue` component information:

```js
vueFormatterPostBody () {
  if (!this.vueFormatters.length) {
    return
  }

  for (let i = this.vueFormatters.length - 1; i >= 0; i--) {
    const formatter = this.vueFormatters[i]

    if (document.getElementsByClassName(formatter.name)) {
      new Vue(formatter)
      this.vueFormatters.splice(i, 1)
    }
  }
}
```

Considering that we will reuse it often, we create a `mixins/table.js` file:

```js
import Vue from 'vue/dist/vue.esm.js'

export default {
  data () {
    return {
      vueFormatters: []
    }
  },

  methods: {
    vueFormatter (obj) {
      const key = `_vue_formatter_${this.vueFormatters.length}`
      this.vueFormatters.push({
        el: `.${key}`,
        name: key,
        ...obj
      })
      return `<div class="${key}"/>`
    },

    vueFormatterPostBody () {
      if (!this.vueFormatters.length) {
        return
      }

      for (let i = this.vueFormatters.length - 1; i >= 0; i--) {
        const formatter = this.vueFormatters[i]

        if (document.getElementsByClassName(formatter.name)) {
          new Vue(formatter)
          this.vueFormatters.splice(i, 1)
        }
      }
    }
  }
}
```

How to use:

```js
formatter: (value, row) => {
  return this.vueFormatter({
    template: '<b-button @click="clickRow(row)">Click</b-button>',
    data: { row },
    methods: {
      clickRow: this.clickRow
    }
  })
}
```

```html
<BootstrapTable
  ref="table"
  :columns="columns"
  :data="data"
  :options="options"
  @onPostBody="vueFormatterPostBody"
/>
```

Online example: https://live.bootstrap-table.com/code/wenzhixin/441

**Things to note:**

Since `new Vue({ template })` requires a `full` build version, if used in webpack, you need to modify all the `vue` import:

```js
import Vue from 'vue/dist/vue.esm.js'
```

Source code: https://github.com/wenzhixin/bootstrap-table-examples/tree/develop/vue-starter

Finally, hope this article can help you.
