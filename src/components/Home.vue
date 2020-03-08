<template>
  <div class="home-container">
    <h1 class="home-container__title">
      Calculadora para la materia de energías
    </h1>

    <div class="home-container__content">
      <router-link
        :to="{name: 'demand'}"
        tag="div"
        class="home-container__content__card"
      >
        <img
          src="@/assets/images/demand.jpg"
          class="home-container__content__card__image"
        />
        <div class="home-container__content__card__text">
          <h2>Cálculo de demanda</h2>
          <p>Determine la demanda máxima para una instalación</p>
        </div>
      </router-link>

      <router-link
        :to="{name: 'cables'}"
        tag="div"
        class="home-container__content__card"
      >
        <img
          src="@/assets/images/cables.png"
          class="home-container__content__card__image"
        />
        <div class="home-container__content__card__text">
          <h2>Cálculo de cables</h2>
          <p>Calcular la sección de un cable</p>
        </div>
      </router-link>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.home-container {
  color: white;
  height: 100%;

  .home-container__title {
    font-size: 3rem;
    text-align: center;
    margin-top: 1rem;
  }

  .home-container__content {
    margin-top: 2rem;
    height: 100%;
    display: flex;
    flex-direction: column;

    .home-container__content__card {
      background-color: rgb(26, 35, 116);
      border-radius: 16px;
      margin-top: 2rem;
      flex-grow: 1;
      display: flex;
      padding: 2rem;
      cursor: pointer;
      box-shadow: 3px 3px 10px rgba(#444, .2);
      transition: 100ms ease-in-out;

      &:hover {
        background-color: lighten(rgb(26, 35, 116), 8%);
        transform: scale(1.05);
      }

      .home-container__content__card__image {
        border-radius: 50%;
        margin-right: 2rem;
        height: 150px;
        width: 150px;
      }

      .home-container__content__card__text {
        h2 {
          font-size: 2rem;
          margin-bottom: .5rem;
        }

        p {
          font-size: 1.5rem;
          color: #777;
        }
      }
    }
  }
}
</style>