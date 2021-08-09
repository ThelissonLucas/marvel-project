<template>
  <header>
    <h1>Projet Marvel</h1>
  </header>
  <Content /> <!-- On inclut le component Content dans notre Appli -->
</template>

<script>
import Content from "./components/Content.vue";

export default {
  name: "App",
  components: {
    Content,
  },
};
</script>
