<template>
  <div vw class="enabled">
    <div vw-access-button class="active"></div>
    <div vw-plugin-wrapper>
      <div class="vw-plugin-top-wrapper"></div>
    </div>
  </div>
</template>

<script>
export default {
  mounted() {
    const script = document.createElement("script");
    script.src = "https://vlibras.gov.br/app/vlibras-plugin.js";
    script.onload = this.initializeVLibras;
    document.body.appendChild(script);
  },

  methods: {
    initializeVLibras() {
      try {
        new window.VLibras.Widget("https://vlibras.gov.br/app");
      } catch (error) {
        console.error("Erro no componente VLibras:", error);
      }
    },
  },
};
</script>
