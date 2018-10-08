<template>
  <button type="button" @click="onclick" v-bind:disabled="clicked">{{ isBomb ? '💣' : cellNeighbours }}</button>
</template>

<script>
export default {
  props: ['cell-neighbours', 'is-bomb'],
  data: () => ({
    clicked: false
  }),
  methods: {
    onclick: function() {
      this.clicked = true;
    }
  }
};
</script>

<style scoped>
button {
  background-color: #bbb;
  border: 2px solid #f2f2f2;
  color: #bbb;
  height: 2rem;
  line-height: 2rem;
  user-select: none;
  width: 2rem;
}

button:disabled {
  background-color: #f2f2f2;
  color: #333;
}
</style>
 