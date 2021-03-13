<template>
  <div id="app">
    <modal
      name="filter-modal"
      style="color: #20262e"
      :min-width="200"
      :min-height="200"
      :scrollable="true"
      :reset="true"
      width="60%"
      height="auto"
    >
      <div style="margin: 15px;">
        <h3>フィルタッ！</h3>
        <div>
          <label>
            <input type="checkbox" v-model="dontHaveFilterFlag" />
            未所持のみ表示するッ！
          </label>
        </div>
        <div>
          <label
            v-for="(s, color) in colors"
            :key="color"
            :style="{ backgroundColor: s }"
          >
            <input type="checkbox" :value="color" v-model="colorFilterFlags" />
            {{ color }}
          </label>
        </div>
      </div>
    </modal>
    <div class="comp-rate">
      <div>
      コンプ率：{{parsented}}
      </div>
      ({{ haveNum }}/{{ totalNum }})
    </div>
    <h2 style="color: red">
    今までありがとな！最後にみんなで終止符を打とうぜ！
    </h2>
    <h2>所持済みSSRユニメット確認手帳ッ！！</h2>
    <span style="font-weight: lighter; font-size: 0.6rem"></span>
    <button @click="show">フィルタッ！</button>
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
import { SSR_UNITS, COLORS } from "./constants";
const doneStorageKey = "doneIDs";
export default {
  name: "App",
  data: function () {
    return {
      colors: COLORS,
      units: SSR_UNITS,
      dontHaveFilterFlag: false,
      colorFilterFlags: Object.keys(COLORS),
    };
  },
  computed: {
    haveNum: function () {
      return this.units.filter((v) => v.done).length;
    },
    totalNum: function () {
      return this.units.length;
    },
    parsented: function() {
      const r = this.haveNum / this.totalNum * 100;
      return Math.floor(r * Math.pow(10, 2)) / Math.pow(10,2) + "%"
    },
    filteredUnits: function () {
      return this.units.filter(this.dontHaveFilter).filter(this.colorFilter);
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
    colorFilter(v) {
      return this.colorFilterFlags.find((c) => c === v.color);
    },
    show() {
      this.$modal.show("filter-modal");
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
  font-size: 14px;
}

ul {
  padding: 0;
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
.comp-rate {
  background-color: rgba(0,0,0,0.7);
  position: fixed;
  bottom: 20px;
  right: 20px;
  text-align: right;
  padding: 3px;
}
</style>

