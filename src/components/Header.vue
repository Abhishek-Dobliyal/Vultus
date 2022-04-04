<template>
  <div class="container d-flex justify-content-center">
    <div class="jumbotron mt-4 text-center">
      <h3 class="title display-3 px-5">{{ title }}</h3>
      <div class="container mt-3">
        <span ref="subTitle" class="subTitle display-6">{{ subTitle }}</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Header",

  props: {
    title: String,
    subTitle: String,
  } 
};
</script>

<style scoped>
.title {
  -webkit-text-stroke-width: 1.5px;
  -webkit-text-stroke-color: black;
  background: -webkit-linear-gradient(120deg, rgb(219, 202, 214), rgb(174, 98, 190));
  background-clip: content-box;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  box-shadow: 5px 5px 5px 3px rgb(183, 148, 199);
  font-family: 'Kanit', sans-serif;
}
.subTitle {
  background: -webkit-linear-gradient(110deg,rgb(150, 110, 183), rgb(150, 121, 183));
  background-clip: content-box;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  word-spacing: 6.5px;
  -webkit-text-stroke-width: 0.5px;
  -webkit-text-stroke-color: black;
  font-family: 'Kanit', sans-serif;
}
</style>
