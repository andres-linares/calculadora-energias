<template>
  <div id="app">
    <!-- <navbar /> -->
    <div class="container mx-auto py-4">
      <transition name="fade" mode="out-in">
        <router-view></router-view>
      </transition> 
    </div>
    <div class="footer">
      <p class="footer__text">Andrés Linares - 160003515</p>
      <p class="footer_Text">Jefferson Martínez - 160003524</p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
}
</script>

<style lang="scss">
#app {
  background-color: #1b1b1b;
  min-height: 100vh;
  width: 100vw;
  position: relative;

  .footer {
    position: absolute;
    bottom: 0;
    right: 0;
    margin-right: 1rem;
    margin-bottom: 1rem;
    text-align: end;
    color: white;
    background-color: #444;
    padding: 1rem 1.6rem;
    border-radius: 12px;
    font-size: 1rem;
    transition: 100ms ease-in-out;

    &:hover {
      background-color: white;
      color: black;
      cursor: default;
    }
  }
}
</style>
