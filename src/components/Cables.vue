<template>
  <div>
    <AppHeader
      title="Cálculo de cables"
      @showHelp="handleShowHelp"
    ></AppHeader>

    <form
      @submit.prevent="calculate"
      class="the-form"
    >
      <div class="column">
        <div class="the-form__section">
          <label>Largo (m)</label>
          <input
            type="number"
            min="0"
            placeholder="Largo del cable"
            v-model="largo"
          />
        </div>

        <div class="the-form__section">
          <label>Demanda Máxima (kW)</label>
          <input
            type="number"
            min="0"
            placeholder="Potencia requerida"
            v-model="potencia"
          />
        </div>

        <div class="the-form__section">
          <label>Caída de tensión (%)</label>
          <input
            type="number"
            min="1"
            max="5"
            placeholder="Potencia requerida"
            v-model="tension"
          />
        </div>

        <div class="the-form__section">
          <label>Temperature (°C)</label>
          <input
            type="number"
            min="0"
            placeholder="Potencia requerida"
            v-model="temperatura"
          />
        </div>
      </div>

      <div class="column">
        <div class="the-form__section">
          <label>Número de tubos protectores verticales</label>
          <select
            class="form-select block w-full mt-1"
            v-model="tubosVerticales"
          >
            <option
              v-for="n in 6"
              v-bind:key="n"
              :value="n"
            >{{ n }}</option>
          </select>
        </div>

        <div class="the-form__section">
          <label>Número de tubos protectores horizontales</label>
          <select
            class="form-select block w-full mt-1"
            v-model="tubosHorizontales"
          >
            <option
              v-for="n in 6"
              v-bind:key="n"
              :value="n"
            >{{ n }}</option>
          </select>
        </div>

        <div class="the-form__section">
          <label>Tipo de sistema</label>
          <select
            class="form-select block w-full mt-1"
            v-model="sistema"
          >
            <option value="1">Monofásico</option>
            <option value="2">Trifásico</option>
            <option value="3">Electroductos magnéticos</option>
          </select>
        </div>

        <div class="the-form__section">
          <label>Tipo de aislamiento</label>
          <select
            class="form-select block w-full mt-1"
            v-model="aislante"
          >
            <option value="1">PVC - PET (mal cable)</option>
            <option value="2">EPR - XLPE (buen cable)</option>
          </select>
        </div>
      </div>

      <button
        class="the-form__submit"
        type="submit"
      >Calcular</button>
    </form>

    <h2 class="the-result" title="Área de la sección transversal del cable">S = {{ seccion }} mm<sup>2</sup></h2>
  </div>
</template>

<script>
import Swal from 'sweetalert2'
import AppHeader from '@/components/AppHeader'

export default {
  components: {
    AppHeader
  },
  data () {
    return {
      potencia: 30,
      largo: 40,
      aislante: 1,
      tension: 3,
      temperatura: 45,
      sistema: 2,
      tubosHorizontales: 1,
      tubosVerticales: 3,
      tabla53: [
        [1.0, 13.5, 12],
        [1.5, 17.5, 15.5],
        [2.5, 24, 21],
        [4, 32, 28],
        [6, 41, 36],
        [10, 57, 50],
        [16, 76, 68],
        [25, 101, 89],
        [35, 125, 111],
        [50, 151, 134],
        [70, 192, 171],
        [95, 232, 207],
        [120, 269, 239],
        [150, 309, 272],
        [185, 353, 310],
        [240, 415, 364],
        [300, 473, 419],
        [400, 566, 502],
        [500, 651, 578]
      ],
      tabla54: [
        [1.0, 18, 16],
        [1.5, 23, 20],
        [2.5, 31, 27],
        [4, 42, 36],
        [6, 54, 48],
        [10, 74, 66],
        [16, 100, 88],
        [25, 132, 116],
        [35, 163, 144],
        [50, 198, 175],
        [70, 252, 222],
        [95, 305, 268],
        [120, 353, 311],
        [150, 400, 353],
        [185, 456, 402],
        [240, 536, 474],
        [300, 617, 545],
        [400, 738, 652],
        [500, 848, 750]
      ],
      tabla56: [
        [10, 1.22, 1.15],
        [15, 1.17, 1.12],
        [20, 1.12, 1.08],
        [25, 1.07, 1.04],
        [35, 0.93, 0.98],
        [40, 0.87, 0.96],
        [45, 0.79, 0.94],
        [50, 0.71, 0.92],
        [55, 0.61, 0.87],
        [60, 0.5, 0.84],
        [65, null, 0.82],
        [70, null, 0.8],
        [75, null, 0.72],
        [80, null, 0.61]
      ],
      tabla59: [
        [1.0, 0.94, 0.91, 0.88, 0.87, 0.86],
        [0.92, 0.87, 0.84, 0.81, 0.8, 0.79],
        [0.85, 0.81, 0.78, 0.76, 0.75, 0.74],
        [0.82, 0.78, 0.74, 0.73, 0.72, 0.72],
        [0.8, 0.76, 0.72, 0.71, 0.7, 0.7],
        [0.79, 0.75, 0.71, 0.7, 0.69, 0.68]
      ],
      tabla517: [
        [1.0, 34, 29, 34],
        [1.5, 23, 20, 23],
        [2.5, 14, 12, 14],
        [4, 8.7, 7.5, 8.7],
        [6, 5.8, 5.1, 5.8],
        [10, 3.5, 3, 3.5],
        [16, 3.31, 1.96, 3.31],
        [25, 1.52, 1.28, 1.52],
        [35, 1.12, 0.96, 1.12],
        [50, 0.82, 0.73, 0.82],
        [70, 0.63, 0.54, 0.63],
        [95, 0.49, 0.42, 0.49],
        [120, 0.41, 0.35, 0.42],
        [150, 0.36, 0.31, 0.37],
        [185, 0.32, 0.27, 0.33],
        [240, 0.26, 0.23, 0.28],
        [300, 0.23, 0.2, 0.24],
        [400, 0.2, 0.18, 0.22],
        [500, 0.19, 0.16, 0.21]
      ],
      seccion: 0
    };
  },
  methods: {
    calculate () {
      let i =
        (this.potencia * 1000) /
        ((this.sistema == "2" ? Math.sqrt(3) : 1) * this.getVoltaje() * 0.9);
      let percent = this.getVoltaje() * (this.tension / 100);

      // Como L > a 40 m. Se hace por caída de tensión y se verifica por capacidad de conducción
      if (this.largo > 40) {
        // Caída de tensión

        let k = percent / (i * (this.largo / 1000));
        let seccion1 = this.getSFrom517(k);

        // Capacidad de conducción
        // Factor de corrección por agrupamiento
        let fa = this.getFa();
        // Factor de corrección por temperatura
        let ft = this.getFt();
        let di = i / (fa * ft);
        let seccion2 = this.getSFrom5354(di);
        // Se escoge la sección más grande
        if (seccion1 > seccion2) this.seccion = seccion1;
        else this.seccion = seccion2;
      } else {
        // Como L <= a 40 m. Se hace por capacidad de conducción y se verifica por caída de tensión
        // Capacidad de conducción
        // Factor de corrección por agrupamiento
        let fa = this.getFa();
        // Factor de corrección por temperatura
        let ft = this.getFt();
        let di = i / (fa * ft);
        let seccion1 = this.getSFrom5354(di);
        // Caída de tensión
        let k = this.tabla517.filter(t => t[0] == seccion1)[0][parseInt(this.sistema)];
        let dv = k * i * (this.largo / 1000);

        // Si el voltaje permitido es menor al porcentaje caída de tensión del voltaje
        if (dv <= percent) {
          this.seccion = seccion1;
        } else {
          percent = percent - 0.1;
          k = percent / (i * (this.largo / 1000));
          this.seccion = this.getSFrom517(k);
          // Buscar una S mayor
          this.showErrorMessage(
            'El cable está mal'
          );
        }
      }
    },
    getVoltaje () {
      // return parseInt(this.sistema) == 1 || parseInt(this.sistema) == 3 ? 120 : 208; // Colombia
      return parseInt(this.sistema) == 1 || parseInt(this.sistema) == 3 ? 220 : 380; // Europa
    },
    getFa () {
      return this.tabla59[this.tubosVerticales - 1][this.tubosHorizontales - 1];
    },
    getFt () {
      let temp = this.tabla56.filter(t => t[0] >= this.temperatura)
      if (temp[0]) {
        return temp[0][
          parseInt(this.aislante)
        ];
      } else {
        this.showErrorMessage(
          'No se puede hacer el cálculo. La temperatura dada no es permitida.'
        );
        return 0;
      }
    },
    getSFrom517 (k) {
      let filteredTable = this.tabla517.filter(
        t => t[parseInt(this.sistema)] <= k
      );
      if (filteredTable[0]) {
        return filteredTable[0][0];
      } else {
        this.showErrorMessage(
          'No se puede hacer el cálculo. La sección requerida no está permitida.'
        );
        return 0;
      }
    },
    getSFrom5354 (di) {
      let filteredTable =
        this.isBadCable()
          ? this.tabla53.filter(t => t[this.getSistema()] >= di + 2)
          : this.tabla54.filter(t => t[this.getSistema()] >= di + 2);
      if (filteredTable[0]) {
        return filteredTable[0][0];
      } else {
        this.showErrorMessage(
          'No se puede hacer el cálculo. La sección requerida no está permitida en las tablas 5.3a y 5.4a.'
        );
        return 0;
      }
    },
    isBadCable () {
      return this.aislante == "1";
    },
    getSistema () {
      return parseInt(this.sistema) == 1 || parseInt(this.sistema) == 3 ? 1 : 2;
    },
    showErrorMessage (msg) {
      Swal.fire({
        title: "¡Error!",
        text: msg,
        type: "error",
        confirmButtonText: "Aceptar"
      });
    },
    handleShowHelp () {
      Swal.fire({
        title: "¿Buscando ayuda?",
        html: `<p class="text-left">Si desea conocer y calcular la sección de un cable, tenga presente:<br>
              - ¿Qué tan largo es el cable?<br>
              - ¿Cuál será el voltaje máximo soportado?<br>
              - ¿Cuál es la demanda máxima (kV)?.
              - ¿Cuál es el porcentaje de caida de la tensión?<br>
              - ¿Cuál será la temperatura que soportara el cable después de la instalación?<br>
              - ¿Cuántos tubos protectores serán instalados?<br>
              - ¿Monofásico, trifásico o electroductos magnéticos?<br>
              - ¿Tipo de aislamiento?.<br><br>
              ¡Finalmente, usted obtendrá <strong>La sección del cable óptima!</strong>`,
        confirmButtonText: "¡Entendido!"
      });
    }
  }
};
</script>
<style lang="scss" scoped>
.the-form {
  background-color: #1a2374;
  padding: 1.8rem 3rem;
  color: white;
  border-radius: 16px;
  box-shadow: 3px 3px 10px rgba(#444, 0.2);
  display: flex;

  .column {
    flex-grow: 1;
  }

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

  .the-form__section {
    margin-bottom: 2rem;

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

.the-result {
  font-size: 2rem;
  background-color: #1a2374;
  margin-top: 2rem;
  color: white;
  width: 300px;
  text-align: center;
  padding: .5rem 1rem;
  border-radius: 16px;
  box-shadow: 3px 3px 10px rgba(#444, 0.2);
}
</style>