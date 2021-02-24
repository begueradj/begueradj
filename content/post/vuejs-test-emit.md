---
title: "Testing emit event in Vue.js using Jest and Vue Test Utils"
date: 2020-07-13T12:33:21+08:00
categories: ["development"]
draft: false
---
Often we need to design a custom component which needs to communicate data with its parent. One way to achieve this is to rely on the `emit` event through the `v-model` directive. The aim of this article is to show how to test `emit` event using Vue Test Utils and Jest.


For this purpose, let us implement the below user interface which consists of a label Vuetify component reflecting what is being typed within the input field:

![file upload](/emit_event.png)

I am going to use Nuxt.js with Vuetify to simplify the task. 
In **pages/index.vue**, we have this:

```html
<template>
  <v-container>
    <v-row no-gutters>
      <v-col cols="4" class="d-flex justify-center align-center">
        {{ firstName }}
      </v-col>
      <v-col cols="8">
        <custom-text-field v-model="firstName" />
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import CustomTextField from '@/components/CustomTextField.vue'
export default {
  components: { CustomTextField },
  data () {
    return {
      firstName: 'begueradj'
    }
  }
}
</script>
```
The content of **components/CustomTextField.vue** file we imported from within **pages/index.vue** is:
```javascript
<template>
  <v-container>
    <v-row>
      <v-col cols="12">
        <v-text-field v-model="firstName" />
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: 'CustomTextField',
  props: {
    value: {
      type: String,
      default: ''
    }
  },
  computed: {
    firstName: {
      get () {
        return this.value
      },
      set (value) {
        this.emitFirstName(value)
      }
    }
  },
  methods: {
    emitFirstName (value) {
      return this.$emit('input', value)
    }
  }

}
</script>
```
Above, we use a `computed` property to track `value` changes which should be emitted right away through `emitFirstName()` method. That is the less cumbersome and most efficient way that allows us to easy test the `emit` event:

```javascript
import { mount } from '@vue/test-utils'
import CustomTextField from '@/components/CustomTextField.vue'

describe('CustomTextField', () => {
  test('it emits expected data', () => {
    const wrapper = mount(CustomTextField)
    expect(wrapper.exists()).toBeTruthy()
    wrapper.vm.emitFirstName('hello') // We emit data
    expect(wrapper.emitted().input[0]).toEqual(['hello']) // Data is emitted with expected value 
  })
})
```
The test will pass:
```javascript
begueradj@begueradj:~/Development/Nuxt/nv1$ yarn test test/CustomTextField.spec.js 
yarn run v1.22.4
$ jest test/CustomTextField.spec.js
 PASS  test/CustomTextField.spec.js
  Logo
    ✓ is a Vue instance (48ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        2.226s
Ran all test suites matching /test\/CustomTextField.spec.js/i.
Done in 3.19s.
```
For the test to run, there are dependencies to install and settings to put in place. You will find the related demo on [this Github repository](https://github.com/begueradj/vuejs-test-emit-event).

Note the presence of **test/setup.js** file as recommended on Vuetify official documentation:
```javascript
import Vue from 'vue'
import Vuetify from 'vuetify'

Vue.use(Vuetify)
```
