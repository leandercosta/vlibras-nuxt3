<template>
  <div>
    <div vw class="enabled">
      <div vw-access-button class="active"></div>
      <div vw-plugin-wrapper>
        <div class="vw-plugin-top-wrapper"></div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { onBeforeUnmount, onMounted } from 'vue'

onMounted(() => {
  const script = document.createElement('script')
  script.src = 'https://vlibras.gov.br/app/vlibras-plugin.js'
  script.id = 'vlibras-script'

  script.onload = () => {
    try {
      new window.VLibras.Widget('https://vlibras.gov.br/app')
      console.log('VLibras inicializado com sucesso')
    } catch (error) {
      console.error('Erro ao inicializar o VLibras:', error)
    }
  }

  document.head.appendChild(script)
})

onBeforeUnmount(() => {
  const script = document.getElementById('vlibras-script')
  if (script) {
    script.remove()
  }
})
</script>
