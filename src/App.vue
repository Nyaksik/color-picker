<template>
  <div class="wrapper main">
    <h1 class="main__title">Выбор цвета по фотографии</h1>
    <h5 class="main__subtitle">
      Загрузите фотографию понравившегося интерьера с вашего компьютера или
      телефона, и мы поможем определить цвет.
    </h5>
    <div class="upload">
      <input id="file" class="upload__input" type="file" />
      <label for="file" class="upload__btn">Выбрать файл</label>
      <label for="file" class="upload__field"
        >Или перетяните изображение сюда</label
      >
    </div>
    <div class="color-pick">
      <img class="color-pick__img" src="" alt="img" />
      <div class="colors">
        <p class="colors__descr">Кликните на фото, чтобы добавить цвет</p>
        <div class="colors__list list">
          <div class="list__mask"></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  // eslint-disable-next-line
  name: "App",
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
  &__img {
    width: 50%;
    height: 400px;
    object-fit: contain;
  }

.colors {
  &__descr {
    width: 150%;
    padding: 15px;

    background-color: rgba($color: $secondColor, $alpha: .2);

    color: #6b6b6b;
  }
}
}
</style>
