<template>
  <div>
    <AppHeader
      title="Cálculo de demanda máxima"
      @showHelp="handleShowHelp"
    ></AppHeader>
    <form
      class="w-full the-form"
      @submit.prevent="calculate"
    >

      <div class="the-form__header">
        <h2 class="text-center">Determinación de la demanda máxima en instalaciones de edificios públicos e instalaciones especiales</h2>
        <div class="the-form__header__buttons">
          <router-link :to="{ name: 'edificios' }"><button>Demanda para edificios de viviendas</button></router-link>
          <router-link :to="{ name: 'demand' }"><button>Demanda para viviendas</button></router-link>
        </div>
      </div>

      <div class="the-form__content">
        <div class="the-form__content__not-given">
          <div class="the-form__section">
            <label>Superficie máxima (m<sup>2</sup>)</label>
            <input
              type="number"
              min="0"
              placeholder="Superficie"
              v-model="maxSurface"
            />
          </div>

          <div class="the-form__section">
            <label>Tipo de edificio</label>
            <select
              class="form-select block w-full mt-1"
              v-model="buildingType"
            >
              <option
                v-for="(type, index) in table111"
                v-bind:value="index"
                v-bind:key="index"
              >
                {{type[0]}}
              </option>
            </select>
          </div>
        </div>
      </div>

      <div class="sm:text-right mx-3 sm:mx-0 text-center mb-4">
        <button
          class="the-form__submit"
          type="submit"
        >Calcular</button>
      </div>
    </form>

    <div class="the-result flex justify-center">
      <p class="the-result__content bg-teal-500 rounded-lg px-5 py-2 text-teal-900 inline-block">
        Demanda máxima de iluminación D <sub>Max I</sub> = {{maxDemand}}W<br>
        Demanda máxima de tomacorrientes D <sub>Max T</sub> = {{outletsDemand}}W<br>
        Máximo número de tomacorrientes = {{outletsQuantity}}<br>
      </p>
    </div>
  </div>
</template>

<script>
import Swal from "sweetalert2";
import AppHeader from "@/components/AppHeader";

export default {
  components: {
    AppHeader
  },
  data () {
    return {
      maxDemand: 0,
      maxSurface: 0,
      buildingType: 0,
      outletsQuantity: 0,
      outletsDemand: 0,
      table110: [
        ["Salas de espectáculo", 10, 0, -1, 1],
        ["Bancos", 20, 0, -1, 1],
        ["Peluquerías y salones de belleza", 30, 0, -1, 1],
        ["Iglesias", 10, 0, -1, 1],
        ["Clubes", 20, 0, -1, 1],
        ["Juzgados y audiencias", 20, 0, -1, 1],
        ["Hospitales", 20, 0, 50000, 0.4, 50001, -1, 0.2],
        ["Hoteles", 10, 0, 20000, 1, 20001, 80000, 0.5, 80001, 100000, 0.4, 100001, -1, 0.3],
        ["Habitaciones de hospedaje", 15, 0, -1, 1],
        ["Restaurantes", 20, 0, -1, 1],
        ["Escuelas", 30, 0, -1, 1]
      ],
      table111: [
        ["Salas de espectáculo", 1, 0, -1, 0.2],
        ["Bancos", 2, 0, -1, 0.7],
        ["Peluquerías y salones de belleza", 4, 0, -1, 0.8],
        ["Iglesias", 1, 0, -1, 0.2],
        ["Clubes", 2, 0, -1, 0.3],
        ["Juzgados y audiencias", 3, 0, -1, 0.4],
        ["Hospitales", 3, 0, 50000, 0.4, 50001, -1, 0.2],
        ["Hoteles", 4, 0, 20000, 1, 20001, 80000, 0.5, 80001, 100000, 0.4, 100001, -1, 0.3],
        ["Habitaciones de hospedaje", 3, 0, 10000, 1, 10001, 40000, 0.5, 40001, 50000, 0.35, 50001, -1, 0.25],
        ["Restaurantes", 2, 0, -1, 0.3],
        ["Escuelas", 2, 0, -1, 0.2]
      ]
    }
  },

  methods: {
    calculate () {
      this.maxDemand = 0;
      this.outletsQuantity = 0;
      this.outletsDemand = 0;

      let k = parseInt(this.buildingType);
      let power = this.table110[k][1];
      let totalPower = power * parseInt(this.maxSurface);

      for (let i = 2; i < this.table110[k].length; i = i + 3) {
        let min = this.table110[k][i];
        let max = this.table110[k][i + 1];
        let fd = this.table110[k][i + 2];
        if (max == -1) {
          this.maxDemand += (totalPower - min) * fd;
          break;
        } else if (this.maxDemand >= min && totalPower < max) {
          this.maxDemand += (totalPower - min) * fd;
          break;
        } else {
          this.maxDemand += max * fd;
        }
      }

      this.outletsQuantity = this.table111[k][1] * Math.ceil(this.maxSurface / 20);

      for (let i = 2; i < this.table111[k].length; i = i + 3) {
        let min = this.table111[k][i];
        let max = this.table111[k][i + 1];
        let fd = this.table111[k][i + 2];
        if (max == -1) {
          this.outletsDemand += (totalPower - min) * fd;
          return;
        } else if (totalPower >= min && totalPower < max) {
          this.outletsDemand += + (totalPower - min) * fd;
          return;
        } else {
          this.outletsDemand += max * fd;
        }
      }
    },

    getIntakeLevel (ms) {
      if (ms <= 80) {
        return this.table11[0];
      } else if (ms <= 140) {
        return this.table11[1];
      } else {
        return this.table11[2];
      }
    },
    showMessage (msg, type = "error") {
      Swal.fire({
        title: type == "error" ? "¡Error!" : "Información",
        text: msg,
        type: type,
        confirmButtonText: "Aceptar"
      });
    },
    handleShowHelp () {
      Swal.fire({
        title: "Ayuda",
        html: `<p class="text-left">Para realizar el cálculo de la demanda máxima <br>
                en edificios públicos tenga en cuenta:<br>
              - La superficie máxima del lugar en donde se realizará la instalación.<br>
              - El tipo de lugar (ver opciones dadas). <br><br>
              Luego el programa mostrará:<br>
              - D Max I = Demanda máxima de iluminación.<br>
              - D Max T = Demanda máxima de tomacorrientes.<br>`,
        confirmButtonText: "Aceptar"
      });
    }
  }
}
</script>

<style lang="scss">
.the-result__content {
  background-color: #1a2374;
  color: white;
  border-radius: 16px;
  box-shadow: 3px 3px 10px rgba(#444, 0.2);
  padding: 0.8rem 1.3rem;
  margin-top: 2rem;
}

.the-form {
  background-color: #1a2374;
  padding: 1.8rem 3rem;
  color: white;
  border-radius: 16px;
  box-shadow: 3px 3px 10px rgba(#444, 0.2);

  .the-form__submit {
    width: 100px;
    height: 100px;
    border-radius: 50%;
    box-shadow: 8px 8px 18px #151c5e, -8px -8px 18px #1f2a8a;
    transition: 100ms ease-in-out;

    &:hover {
      background-color: lighten($color: #1a2374, $amount: 3%);
      transform: scale(1.1);
    }
  }

  .the-form__header {
    margin-bottom: 2rem;
    .the-form__header__buttons {
      display: flex;
      justify-content: space-around;
      margin-top: 0.8rem;

      button {
        background-color: lighten($color: #1a2374, $amount: 20%);
        color: #ccc;
        padding: 0.5rem 1rem;
        border-radius: 10px;

        &.active {
          background: linear-gradient(
            90deg,
            rgba(10, 77, 23, 1) 0%,
            rgba(6, 154, 184, 1) 100%
          );
          color: white;
          outline: none;
        }
      }
    }
  }

  .the-form__content {
    .the-form__content__not-given {
      display: flex;
      justify-content: space-around;
    }

    .the-form__section {
      margin-bottom: 2rem;

      .form-label {
        color: white;
      }

      input {
        border: none;
      }

      label {
        display: block;
        margin-bottom: 0.4rem;
        font-size: 1.2rem;
        color: rgb(182, 181, 181);
      }

      input {
        font-size: 1.5rem;
        color: white;
        padding: 0.5rem 1rem;
        background-color: transparent;
        width: 350px;
        box-shadow: 8px 8px 18px #151c5e, -8px -8px 18px #1f2a8a;
        border-radius: 8px;
        transition: 100ms ease-in-out;

        &::-webkit-inner-spin-button {
          display: none;
        }

        &:hover {
          background-color: lighten($color: #1a2374, $amount: 3%);
        }

        &:focus {
          transform: scale(1.1);
          outline: none;
          background-color: lighten($color: #1a2374, $amount: 3%);
        }
      }

      select {
        font-size: 1.5rem;
        color: white;
        padding: 0.5rem 1rem;
        background-color: #1a2374;
        border: none;
        width: 350px;
        box-shadow: 8px 8px 18px #151c5e, -8px -8px 18px #1f2a8a;
        transition: 100ms ease-in-out;

        &:hover {
          background-color: lighten($color: #1a2374, $amount: 3%);
        }

        &:focus {
          transform: scale(1.1);
          border: none;
          background-color: lighten($color: #1a2374, $amount: 3%);
        }
      }
    }
  }
}
</style>