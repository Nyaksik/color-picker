<template>
  <div class="wrapper main">
    <h1 class="main__title">Выбор цвета по фотографии</h1>
    <h5 class="main__subtitle">
      Загрузите фотографию понравившегося интерьера с вашего компьютера или
      телефона, и мы поможем определить цвет.
    </h5>
    <div class="upload">
      <input
        id="file"
        class="upload__input"
        type="file"
        accept=".pdf,.jpg,.jpeg,.png"
        @change="onFileChange"
      />
      <label for="file" class="upload__btn">Выбрать файл</label>
      <div class="upload__field" @dragover.prevent @drop.prevent="dropFile">
        Или перетяните изображение сюда
      </div>
    </div>
    <div class="color-pick">
      <div class="color-pick__canvas">
        <canvas
          ref="canvas"
          height="500px"
          width="500px"
          @click="clickHandler"
          @mousemove="mouse"
        ></canvas>
      </div>
      <div class="colors">
        <p class="colors__descr">Кликните на фото, чтобы добавить цвет</p>
        <div class="colors__list">
          <ColorBlock
            @removeColor="removeColor"
            v-for="color in colors"
            :key="color"
            :color="color"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
/* eslint-disable */
import chroma from "chroma-js";
import ColorBlock from "@/components/ColorBlock";

export default {
  name: "App",

  components: {
    ColorBlock,
  },
  data() {
    return {
      image: "",
      canvas: "",
      context: "",
      hex: "",
      colors: [],
    };
  },
  mounted() {
    this.canvas = this.$refs.canvas;
    this.canvas.width = 500;
    this.canvas.height = 500;
    this.context = this.canvas.getContext("2d");
  },
  methods: {
    dropFile(e) {
      this.onFileChange(e);
    },
    removeColor(id) {
      this.colors = this.colors.filter((it) => it.id !== id);
    },
    createImage(file) {
      const image = new Image();
      const reader = new FileReader();
      reader.onload = (e) => {
        new Promise((resolve) => {
          resolve(e.target.result);
        })
          .then((res) => {
            image.src = res;
            return res;
          })
          .then(() => {
            this.context.drawImage(
              image,
              0,
              0,
              this.canvas.width,
              this.canvas.height
            );
          });
      };
      reader.readAsDataURL(file);
    },
    onFileChange(e) {
      const files = e.target.files || e.dataTransfer.files;

      if (!files.length) {
        return;
      }
      this.colors = [];
      this.createImage(files[0]);
      if (this.context) {
        this.context.clearRect(0, 0, this.canvas.width, this.canvas.height);
      }
    },
    mouse(e) {
      const x = e.layerX;
      const y = e.layerY;
      console.log(x, y - 250);
    },
    clickHandler(e) {
      const position = this.findPos(this.canvas);
      const x = e.pageX - position.x;
      const y = e.pageY - position.y;
      const canvas = this.canvas.getContext("2d");
      const imageData = canvas.getImageData(x, y, 1, 1).data;
      const rgb = {
        red: imageData[0],
        green: imageData[1],
        blue: imageData[2],
      };
      const hex = chroma([rgb.red, rgb.green, rgb.blue]).hex();

      this.colors.push({ id: Date.now(), color: hex });
    },
    findPos(obj) {
      let current_left = 0,
        current_top = 0;
      if (obj.offsetParent) {
        do {
          current_left += obj.offsetLeft;
          current_top += obj.offsetTop;
        } while ((obj = obj.offsetParent));
        return { x: current_left, y: current_top };
      }
      return undefined;
    },
  },
};
</script>

<style lang="scss">
@import url("./style/fonts.scss");

$primeColor: #2c2c2c;
$secondColor: #d2d2d2;

#app {
  font-family: "Montserrat", sans-serif;
  font-size: 16px;
  color: $primeColor;
}

.wrapper {
  max-width: 1080px;
  margin: auto;
  overflow: hidden;
}

.main {
  &__title {
    font-family: "EB Garamond", serif;
    font-size: 80px;
    line-height: 80px;
  }

  &__subtitle {
    font-size: 16px;
    line-height: 24px;
  }

  &__title,
  &__subtitle {
    text-align: start;
  }
}

.upload {
  display: flex;
  align-items: center;
  justify-content: flex-start;
  gap: 10px;

  font-size: 18px;

  &__input {
    display: none;
  }

  &__btn {
    color: white;
    background-color: $primeColor;
    padding: 15px 30px;
    white-space: nowrap;
  }

  &__field {
    border: $secondColor dashed 2px;
    padding: 13px 30px;
    width: 50%;
  }

  &__btn,
  &__field {
    text-align: center;
  }
}

.color-pick {
  display: flex;
  align-content: center;
  justify-content: flex-start;
  gap: 10px;

  &__canvas {
    width: 500px;
    height: 500px;
    canvas {
      cursor: url("assets/pipette.png"), pointer;
    }
  }

  .colors {
    &__descr {
      width: 150%;
      padding: 15px;

      background-color: rgba($color: $secondColor, $alpha: 0.2);

      color: #6b6b6b;
    }
    &__list {
      display: flex;
      flex-wrap: wrap;
      gap: 10px;
    }
  }
}
</style>
