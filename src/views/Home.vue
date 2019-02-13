<template>
  <div class="home container text-center">
    <img src="../assets/flower2.jpg" class="img-responsive" alt="Responsive image">
       <ChatRoom/> 
  </div>
</template>

<script>
// @ is an alias to /src
import ChatRoom from '@/components/ChatRoom.vue'

export default {
  name: 'home',
  components: {
    ChatRoom
  }
}
</script>
<style  scoped>
@import "https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css";
.img-responsive{
  width: 100%
}
</style>
