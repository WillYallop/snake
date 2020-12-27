<template>
  <div class="pageCon">

    <SnakeComp/>

  </div>
</template>

<script>
import SnakeComp from '@/components/SnakeComp'

export default {
  components: {
    SnakeComp
  }
}
</script>

<style>
.pageCon {
  min-height: 100vh;
  padding: 40px;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-content: center;
}
</style>
