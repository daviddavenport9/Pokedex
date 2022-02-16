<template>
  <header class="header">
    <h1>Welcome to the Pokemon Homepage!</h1>
    <img src="@/assets/Pokemon.png" width="205" height="82" />
  </header>
</template>

<script>
export default {};
</script>

<style scoped>
h1 {
  color: white;
  font-size: 30px;
  text-align: left;
  margin-top: 20px;
  line-height: 20%;
}
.header {
  text-align: center;
  background: #f30814;
  padding: 10px;
  margin-top: -20px;
  margin-right: -20px;
  margin-left: -10px;
  width: 100%;
}

img {
  display: inline-block;
}
</style>
