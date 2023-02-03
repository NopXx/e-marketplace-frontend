<template>
  <div>
    <lottie-vue-player
      src="/json/404.json"
      :theme="options.theme"
      :player-size="options.playerSize"
      :player-controls="false"
      :autoplay="true"
      :loop="true"
      style="width: 100%; height: 65vh"
    >
    </lottie-vue-player>
    
  </div>
</template>
  
<script>
export default {
  name: '404NotFound',
  data() {
    return {
      options: {
        minimizable: false,
        playerSize: 'standard',
        backgroundColor: '#3D4852',
        backgroundStyle: 'color',
        theme: {
          controlsView: 'standard',
          active: 'dark',
          light: {
            color: '#3D4852',
            backgroundColor: '#fff',
            opacity: '0.7',
          },
          dark: {
            color: '#3D4852',
            backgroundColor: '#202020',
            opacity: '0.7',
          },
        },
      },
    }
  },
}
</script>