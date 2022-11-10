<template>
  <div>
    <section id="form">Form</section>
    <button id="button-open" class="Hide" @click="OpenButton()">
      IconOpen
    </button>
    <button id="button-close" @click="CloseButton()">IconClose</button>

    <section id="map">Map</section>
  </div>
</template>

<script>
export default {
  methods: {
    OpenButton() {
      document.getElementById("button-open").classList.add("Hide");
      document.getElementById("button-close").classList.remove("Hide");
      document.getElementById("form").classList.remove("Hide");
    },
    CloseButton() {
      document.getElementById("button-close").classList.add("Hide");
      document.getElementById("button-open").classList.remove("Hide");
      document.getElementById("form").classList.add("Hide");
    },
  },
};
</script>

<style>
.Hide {
  display: none;
}
</style>
