<template>
  <div class="color">
    <div class="color__color" :style="`background-color: ${color.color}`"></div>
    <p class="color__title"><strong>HEX</strong> {{ color.color }}</p>
    <div class="color__trash" @click="clickHandler(color.id)">
      <img src="../assets/trash.png" alt="trash" />
    </div>
  </div>
</template>

<script>
export default {
  props: {
    color: Object,
  },
  methods: {
    clickHandler(id) {
      this.$emit('removeColor', id);
    },
  },
};
</script>

<style lang="scss">
.color {
  position: relative;
  width: 190px;

  font-size: 14px;
  &__color {
    width: 150px;
    height: 110px;
    mask-image: url("../assets/vector.svg");
    mask-repeat: no-repeat;
  }
  &__title {
    border: rgba($color: #6b6b6b, $alpha: 0.2) 1px solid;

    color: #2c2c2c;

    padding: 0 0 5px 5px;
    strong {
      font-weight: 500;
      color: #6b6b6b;
    }
  }
  &__trash {
    position: absolute;
    right: 0;
    top: 0;

    display: flex;
    align-items: center;
    justify-content: center;

    width: 30px;
    height: 30px;

    background-color: #2c2c2c;
    background-size: contain;

    border-radius: 8px;

    cursor: pointer;
    img {
      width: 15px;
      height: 15px;
      filter: invert(1);
    }
  }
}
</style>
