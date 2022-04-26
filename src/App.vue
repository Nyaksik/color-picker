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
      <div class="upload__field" @dragover.prevent @drop.prevent="onFileChange">
        Или перетяните изображение сюда
      </div>
    </div>
    <div class="color-pick" @mousemove="loupeMove" v-show="isImage">
      <div class="color-pick__canvas canvas">
        <canvas
          ref="canvas"
          height="500px"
          width="500px"
          @click="clickHandler"
        />
        <canvas class="canvas__loupe" ref="canvasLoupe" v-show="isMouseEnter" />
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
import chroma from 'chroma-js';
// eslint-disable-next-line
import ColorBlock from '@/components/ColorBlock';
import canvasCover from '@/helper/canvasCover';

export default {
  components: {
    ColorBlock,
  },
  data() {
    return {
      canvas: '',
      isImage: false,
      isMouseEnter: false,
      canvasLoupe: '',
      context: '',
      colors: [],
    };
  },
  mounted() {
    this.canvas = this.$refs.canvas;
    this.canvasLoupe = this.$refs.canvasLoupe;
    this.canvas.width = 500;
    this.canvas.height = 500;
    this.context = this.canvas.getContext('2d');
  },
  methods: {
    loupeMove(e) {
      this.isMouseEnter = true;
      const context = this.canvasLoupe.getContext('2d');
      const position = this.findPos(this.canvas);
      const xPosition = e.pageX - position.x;
      const yPosition = e.pageY - position.y;
      const xMin = Math.min(xPosition, 500);
      const yMin = Math.min(yPosition, 500);

      if (
        xPosition > 500
        || yPosition > 500
        || xPosition < 0
        || yPosition < 0
      ) {
        this.isMouseEnter = false;
      }

      this.canvasLoupe.style.left = `${xMin - 10}px`;
      this.canvasLoupe.style.top = `${yMin + 15}px`;
      context.drawImage(
        this.canvas,
        xMin - 10,
        yMin + 15,
        5,
        5,
        0,
        0,
        100,
        100,
      );
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
            canvasCover(
              this.context,
              image,
              0,
              0,
              this.canvas.width,
              this.canvas.height,
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
      this.isImage = true;
      if (this.context) {
        this.context.clearRect(0, 0, this.canvas.width, this.canvas.height);
      }
    },
    clickHandler(e) {
      const position = this.findPos(this.canvas);
      const x = e.pageX - position.x;
      const y = e.pageY - position.y;
      const imageData = this.context.getImageData(x, y, 1, 1).data;
      const rgb = {
        red: imageData[0],
        green: imageData[1],
        blue: imageData[2],
      };
      const hex = chroma([rgb.red, rgb.green, rgb.blue]).hex();

      this.colors.push({ id: Date.now(), color: hex });
    },
    findPos(obj) {
      let currentLeft = 0;
      let currentTop = 0;
      if (obj.offsetParent) {
        do {
          currentLeft += obj.offsetLeft;
          currentTop += obj.offsetTop;
          // eslint-disable-next-line
        } while ((obj = obj.offsetParent));
        return { x: currentLeft, y: currentTop };
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
    margin-bottom: 64px;
  }

  &__subtitle {
    font-size: 16px;
    line-height: 24px;
    margin-bottom: 56px;
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

  margin-bottom: 56px;

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

  .canvas {
    position: relative;
    width: 500px;
    height: 500px;

    cursor: url("assets/pipette.png"), pointer;
    &__loupe {
      position: absolute;
      border: $primeColor 2px solid;
      border-radius: 50%;
      width: 16px;
      height: 16px;
    }
  }

  .colors {
    &__descr {
      width: 100%;
      margin: 0 0 26px 0;
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
