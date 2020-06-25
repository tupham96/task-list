<template>
  <div>
    <header>
      <i class="fas fa-bars"></i>
      <h2>Task Management</h2>
    </header>
    <div class="container d-flex justify-content-center">
      <app-home></app-home>
    </div>
  </div>
</template>
  
<script>
import Home from "./components/Home.vue";

export default {
  components: {
    appHome: Home
  },
  data() {
    return {
      id: null,
      title: "",
      is_completed: false,
    };
  }
};
</script>
  
<style lang="scss">
@import "~bootstrap/dist/css/bootstrap.css";

body {
  margin: 0;
  background: rgba(210, 210, 210, 0.22);

  header {
    background: #d65d5d;
    padding: 15px;
    display: flex;
    align-items: center;

    h2 {
      font-size: 24px;
      line-height: 28px;
      color: #ffffff;
      font-weight: 600;
      text-align: center;
      margin: 0 auto;
    }

    i {
      color: #ffffff;
      font-size: 20px;
    }
  }
}
</style>
