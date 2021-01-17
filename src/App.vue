<template>
  <div id="app">
    <h2 style="color: red">
      今は6部までの繋ぎ！今のうちに未所持SSRを一挙確認！
    </h2>
    <h2>所持済みSSRユニメット確認手帳ッ！！({{ haveNum }}/{{ totalNum }})</h2>
    <span style="font-weight: lighter; font-size: 0.6rem"></span>
    <ul>
      <li v-for="unit in filteredUnits" :key="unit.name">
        <label>
          <input
            type="checkbox"
            v-on:change="toggle(unit)"
            v-bind:checked="unit.done"
          />
          <del v-if="unit.done" :style="{ color: colors[unit.color] }">
            {{ unit.name }}／{{ unit.abi }}
          </del>
          <span v-else :style="{ color: colors[unit.color] }">
            {{ unit.name }}／{{ unit.abi }}
          </span>
        </label>
      </li>
    </ul>
  </div>
</template>

<script>
import { SSR_UNITS } from "./constants";
const doneStorageKey = "doneIDs";
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
      units: SSR_UNITS,
      dontHaveFilterFlag: false,
    };
  },
  computed: {
    haveNum: function () {
      return this.units.filter((v) => v.done).length;
    },
    totalNum: function () {
      return this.units.length;
    },
    filteredUnits: function () {
      return this.units.filter(this.dontHaveFilter);
    },
  },
  mounted: function () {
    this.$nextTick(function () {
      this.applyDoneData();
    });
  },
  methods: {
    toggle: function (unit) {
      unit.done = !unit.done;
      this.updateDoneData();
    },
    updateDoneData: function () {
      const idx = this.units
        .map((v) => (v.done ? v.id : ""))
        .filter((v) => v !== "");
      window.localStorage.setItem(doneStorageKey, JSON.stringify(idx));
    },
    applyDoneData: function () {
      const ids = JSON.parse(window.localStorage.getItem(doneStorageKey));
      ids.forEach((v) => (this.units.find((u) => u.id === v).done = true));
    },
    /** 未所持フィルタ */
    dontHaveFilter(v) {
      return this.dontHaveFilterFlag ? !v.done : true;
    },
  },
};
</script>

<style>
body {
  background: #20262e;
  color: white;
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
  list-style-type: none;
}

h2 {
  font-weight: bold;
  margin-bottom: 15px;
}

del {
  color: rgba(0, 0, 0, 0.3);
}
</style>

