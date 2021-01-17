<template>
  <div id="app">
    <h2 style="color: red">
      今は6部までの繋ぎ！今のうちに未所持SSRを一挙確認！
    </h2>
    <h2>
      所持済み期間限定ユニメット確認手帳ッ！！({{ haveNum }}/{{ totalNum }})
    </h2>
    <span style="font-weight: lighter; font-size: 0.6rem"></span>
    <ol>
      <li v-for="todo in todos" :key="todo.name">
        <label>
          <input
            type="checkbox"
            v-on:change="toggle(todo)"
            v-bind:checked="todo.done"
          />
          <del v-if="todo.done" :style="{ color: colors[todo.color] }">
            {{ todo.name }}／{{ todo.abi }}
          </del>
          <span v-else :style="{ color: colors[todo.color] }">
            {{ todo.name }}／{{ todo.abi }}
          </span>
        </label>
      </li>
    </ol>
  </div>
</template>

<script>
import { SSR_UNITS } from "./constants";
export default {
  name: "App",
  data: function () {
    return {
      colors: {
        白: "#ffffcc",
        黒: "#9242a0",
        赤: "#db402c",
        青: "#28a1d1",
        緑: "#4e9b32",
        金: "#dab300",
        銀: "#aaaab5",
      },
      todos: SSR_UNITS,
    };
  },
  computed: {
    haveNum: function () {
      return this.todos.filter((v) => v.done).length;
    },
    totalNum: function () {
      return this.todos.length;
    },
  },
  mounted: function () {
    this.$nextTick(function () {
      this.applyDoneData();
    });
  },
  methods: {
    toggle: function (todo) {
      todo.done = !todo.done;
      this.updateDoneData();
    },
    updateDoneData: function () {
      const idx = this.todos
        .map((v, i) => (v.done ? i : -1))
        .filter((v) => v > -1);
      window.localStorage.setItem("doneIDX", JSON.stringify(idx));
    },
    applyDoneData: function () {
      const doneIDX = JSON.parse(window.localStorage.getItem("doneIDX"));
      doneIDX.forEach((v) => (this.todos[v].done = true));
    },
  },
};

</script>

<style>
body {
  background: #20262e;
  color: white;
  padding: 20px;
  font-family: Helvetica;
}

#app {
  background: #000000;
  border-radius: 4px;
  padding: 20px;
  transition: all 0.2s;
  font-weight: bold;
}

li {
  margin: 8px 0;
}

h2 {
  font-weight: bold;
  margin-bottom: 15px;
}

del {
  color: rgba(0, 0, 0, 0.3);
}
</style>

