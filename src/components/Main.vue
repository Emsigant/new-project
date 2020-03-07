<template>
  <div class="hello">
    <div class="list">
      <div v-for="item in list" :key="item.id" class="item">
        <input class="check" type="checkbox" v-model="item.checked" />
        <span
          @click="handleShowEdit(item, true)"
          v-show="!item.showEdit"
          class="text"
          :class="item.checked ? 'delete':''"
        >{{item.text}}</span>
        <input
          class="text"
          v-show="item.showEdit"
          type="text"
          :value="item.text"
          @input="handelEdit($event, item)"
          @blur="item.showEdit = false"
          ref="itemInput"
        />
      </div>
    </div>
    <div class="item">
      <input type="checkbox" class="check" />
      <input
        class="text"
        ref="input"
        placeholder="输入待办事项"
        type="text"
        @keyup="handleKeyUp"
        v-model="val"
      />
    </div>
  </div>
</template>

<script>
export default {
  name: "Main",
  props: {
    msg: String
  },
  data() {
    return {
      list: [],
      val: ""
    };
  },
  methods: {
    handleKeyUp(e) {
      const { val, list } = this;
      const { keyCode } = e;
      if (keyCode === 13) {
        if (val.trim()) {
          list.push({
            checked: false,
            text: val,
            id: list.length,
            showEdit: false
          });
          this.val = "";
        }
      }
      if (keyCode === 8) {
        if (val === "") {
          const itemInputs = this.$refs.itemInput;
          const lastInput = itemInputs[itemInputs.length - 1];
          const input = this.$refs.input;
          input.blur();
          if (list.length) {
            list[list.length - 1].showEdit = true;
            this.$nextTick().then(() => {
              lastInput.focus();
            });
          }
        }
      }
    },
    handelEdit(e, item) {
      const v = e.target.value;
      const itemInputs = this.$refs.itemInput;
      const { list } = this;
      item.text = v;
      if (v === "") {
        const index = list.findIndex(i => i.id === item.id);
        list.splice(index, 1);
        this.$nextTick().then(() => {
          if (itemInputs.length) {
            const last = itemInputs[itemInputs.length - 1];
            list[list.length - 1].showEdit = true;
            this.$nextTick().then(() => {
              last.focus();
            });
          } else {
            this.$refs.input.focus();
          }
        });
      }
    },
    handleShowEdit(item, showEdit) {
      const index = this.list.findIndex(i => i.id === item.id);
      item.showEdit = showEdit;
      this.$nextTick().then(() => {
        this.$refs.itemInput[index].focus();
      });
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.hello {
  padding: 12px;
  font-size: 16px;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
    Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
}
.list {
  display: flex;
  flex-direction: column;
}
.item {
  display: flex;
  align-items: center;
  line-height: 20px;
  margin-bottom: 8px;
}
.item .check {
  flex: none;
}
.item .text {
  margin-left: 6px;
  flex: 1;
  height: 20px;
}
.item .text.delete {
  text-decoration: line-through;
}
input {
  font-family: inherit;
  text-decoration: none;
  border: none;
  margin: 0;
  padding: 0;
  font-size: 16px;
  outline: none;
}
</style>