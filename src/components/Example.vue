<template>
  <div id="app">
    <h1>Vue + TypeScript + Protobuf Demo</h1>
    <button @click="serializeData">
      Serialize
    </button>
    <button @click="deserializeData">
      Deserialize
    </button>
    <div v-if="buffer">
      <h3>Serialized Buffer:</h3>
      <p>{{ buffer }}</p>
    </div>
    <div v-if="person">
      <h3>Deserialized Person:</h3>
      <pre>{{ person }}</pre>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import { Person } from '../protobuf/message' // 导入生成的消息类

export default defineComponent({
  data() {
    return {
      buffer: '',
      person: null
    }
  },
  methods: {
    serializeData() {
      const person: Person = Person.create({
        name: 'wbb',
        id: 123,
        email: 'wbb@example.com'
      })
      this.buffer = Person.encode(person).finish()
    },
    deserializeData() {
      if (this.buffer) {
        const decodedPerson = Person.decode(this.buffer)
        this.person = decodedPerson
      }
    }
  }
})
</script>

<style>
#app {
  text-align: center;
}
</style>
