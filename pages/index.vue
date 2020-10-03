<template>
  <v-container>
    <h2>{{ `${numToChar(turn)}のターンです` }}</h2>
    <div class="row" v-for="y of LINE_NUM" :key="y">
      <div
        class="cell"
        v-for="x of LINE_NUM"
        :key="x"
        @click="onCellClick(x - 1, y - 1)"
      >
        <div>{{ getCellChar(x - 1, y - 1) }}</div>
      </div>
    </div>
    <v-dialog v-model="dialog" @click:outside="reset" max-width="290">
      <v-card>
        <v-card-title>
          <h2>{{ dialogMessage }}</h2>
        </v-card-title>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
export default {
  data: function () {
    return {
      turn: -1,
      cells: Array(9),
      LINE_NUM: 3,
      dialog: false,
      dialogMessage: "",
    };
  },
  mounted() {
    this.reset();
  },
  methods: {
    reset() {
      this.turn = -1;
      this.dialog = false;
      this.dialogMessage = "";
      this.cells.fill(0);
    },
    onCellClick(x, y) {
      if (0 === this.getCellNum(x, y)) {
        this.cells[y * this.LINE_NUM + x] = this.turn;
        if (this.checkLines()) {
          this.dialogMessage = `${this.numToChar(this.turn)}の勝ちです`;
          this.dialog = true;
        } else {
          this.turn *= -1;
          if (this.isFullArray()) {
            this.dialogMessage = `引き分けです`;
            this.dialog = true;
          }
        }
      }
    },
    getCellNum(x, y) {
      return this.cells[y * this.LINE_NUM + x];
    },
    numToChar(num) {
      switch (num) {
        case 0:
          return " ";
          break;
        case 1:
          return "◯";
          break;
        case -1:
          return "×";
      }
      return " ";
    },
    getCellChar(x, y) {
      return this.numToChar(this.getCellNum(x, y));
    },
    isFullArray() {
      let ret = true;
      for (let i = 0; i < this.cells.length; i++) {
        if (this.cells[i] === 0) {
          ret = false;
          break;
        }
      }
      return ret;
    },
    isCompleteArray(arr) {
      let ret = true;
      for (let i = 0; i < arr.length; i++) {
        if (arr[0] !== arr[i]) {
          ret = false;
          break;
        }
      }
      return ret && arr[0] !== 0;
    },
    checkLines() {
      let completed = false;
      // 横
      for (let y = 0; y < this.LINE_NUM; y++) {
        const line = [];
        for (let x = 0; x < this.LINE_NUM; x++) {
          line.push(this.getCellNum(x, y));
        }
        if (this.isCompleteArray(line)) {
          completed = true;
        }
      }
      // 縦
      for (let x = 0; x < this.LINE_NUM; x++) {
        const line = [];
        for (let y = 0; y < this.LINE_NUM; y++) {
          line.push(this.getCellNum(x, y));
        }
        if (this.isCompleteArray(line)) {
          completed = true;
        }
      }
      // 斜め
      {
        const line = [];
        for (let x = 0; x < this.LINE_NUM; x++) {
          line.push(this.getCellNum(x, x));
        }
        if (this.isCompleteArray(line)) {
          completed = true;
        }
      }
      // 斜め
      {
        const line = [];
        for (let x = 0; x < this.LINE_NUM; x++) {
          line.push(this.getCellNum(x, this.LINE_NUM - x - 1));
        }
        if (this.isCompleteArray(line)) {
          completed = true;
        }
      }
      return completed;
    },
  },
  computed: {},
};
</script>
<style scoped>
.cell {
  border: 1px solid;
  text-align: center;
  font-size: 2rem;
  width: 4em;
  height: 4em;
  line-height: 4em;
}
</style>