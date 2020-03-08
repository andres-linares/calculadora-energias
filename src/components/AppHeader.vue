<template>
  <div class="pb-4 px-3 sm:px-0 flex items-center justify-between the-header">
    <router-link
      :to="{name: 'home'}"
      title="Volver al inicio"
      class="the-header__button bg-gray-300 hover:bg-gray-400 text-gray-800 py-2 px-4 rounded-lg inline-flex items-center focus:outline-none focus:shadow-outline"
    >
      <svg
        class="fill-current w-4 h-4 mr-2"
        xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 24 24"
        width="24"
        height="24"
      >
        <path d="M5.41 11H21a1 1 0 0 1 0 2H5.41l5.3 5.3a1 1 0 0 1-1.42 1.4l-7-7a1 1 0 0 1 0-1.4l7-7a1 1 0 0 1 1.42 1.4L5.4 11z" /></svg>
      <span>Volver</span>
    </router-link>
    <h2 class="the-header__title">
      {{ title }}
    </h2>

    <button
      @click.prevent="showHelp"
      title="Mostrar ayuda"
      class="the-header__button bg-gray-300 hover:bg-gray-400 text-gray-800 py-2 px-2 rounded-lg inline-flex items-center focus:outline-none focus:shadow-outline"
    >
      <svg
        xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 24 24"
        width="24"
        height="24"
        class="fill-current"
      >
        <path d="M12 22a10 10 0 1 1 0-20 10 10 0 0 1 0 20zm0-2a8 8 0 1 0 0-16 8 8 0 0 0 0 16zM10.59 8.59a1 1 0 1 1-1.42-1.42 4 4 0 1 1 5.66 5.66l-2.12 2.12a1 1 0 1 1-1.42-1.42l2.12-2.12A2 2 0 0 0 10.6 8.6zM12 18a1 1 0 1 1 0-2 1 1 0 0 1 0 2z" /></svg>
    </button>
  </div>
</template>

<script>
export default {
  props: ['title'],
  methods: {
    showHelp () {
      this.$emit('showHelp');
    }
  }
}
</script>

<style lang="scss" scoped>
.the-header {
  background-color: #1a2374;
  margin-bottom: 2rem;
  border-radius: 16px;
  padding: 0.5rem 1rem;
  box-shadow: 3px 3px 10px rgba(#444, 0.2);
  color: white;

  .the-header__title {
    font-size: 2rem;
  }

  .the-header__button {
    background-color: #1a2374;
    color: white;
    box-shadow: 8px 8px 18px #151c5e, -8px -8px 18px #1f2a8a;

    transition: 100ms ease-in-out;

    &:hover {
      background-color: lighten($color: #1a2374, $amount: 3%);
      transform: scale(1.1);
    }
  }
}
</style>