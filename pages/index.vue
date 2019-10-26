<template>
  <div class="container">
      <app-fifteen />
  </div>
</template>

<script>
import Fifteen from '~/components/Fifteen.vue'

export default {
    components: {
      'app-fifteen': Fifteen
    }
}
</script>

<style>
    .container {
        margin: 0 auto;
        min-height: 100vh;
        display: flex;
        justify-content: center;
        align-items: center;
        text-align: center;
        background-color: #47494e59;
    }
</style>
