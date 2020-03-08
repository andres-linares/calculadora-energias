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
        <h2 class="text-center">Determinación de la demanda máxima en instalaciones domiciliarias (viviendas unifamiliares)</h2>
        <div class="the-form__header__buttons">
          <button
            @click="setGivenInstalledPower(true)"
            :class="{ active: isGivenInstalledPower }"
          >Conozco los valores</button>
          <button
            @click="setGivenInstalledPower(false)"
            :class="{ active: !isGivenInstalledPower }"
          >Calcular las potencias</button>
          <router-link :to="{ name: 'edificios' }"><button>Demanda para edificios de viviendas</button></router-link>
          <router-link :to="{ name: 'publicos' }"><button>Demanda para edificios públicos</button></router-link>
        </div>
      </div>

      <div class="the-form__content">
        <transition name="fade-shrink">
          <div
            class="the-form__content__not-given"
            v-if="!isGivenInstalledPower"
          >
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
              <label>Tipo de iluminación</label>
              <select
                class="form-select block w-full mt-1"
                v-model="lightingType"
              >
                <option value="inc">Incandescente</option>
                <option value="flu">Fluorescente</option>
              </select>
            </div>
          </div>
        </transition>

        <div class="the-form__section flex flex-wrap items-center md:-mx-3 mb-2">

          <fieldset
            class="w-full md:w-1/2 px-3 sm:px-0 md:px-3 my-2"
            v-if="isGivenInstalledPower"
          >
            <legend class="w-full text-center form-label">Potencias instaladas</legend>
            <div class=" flex-wrap -mx-2 mb-2">
              <div class="w-full md:w-1/2 px-2 mb-6 md:mb-2">
                <label class="block">
                  <span class="form-label">Potencia instalada en iluminación (W)</span>
                  <input
                    class="form-input mt-1 block w-full"
                    type="number"
                    min="0"
                    placeholder="Potencia instalada"
                    v-model="lightningPower"
                  />
                </label>
              </div>
              <div class="w-full md:w-1/2 px-2 mb-6 md:mb-2">
                <label class="block">
                  <span class="form-label">
                    Potencia instalada en tomacorriente (W)
                  </span>
                  <input
                    class="form-input mt-1 block w-full"
                    type="number"
                    min="0"
                    placeholder="Potencia instalada"
                    v-model="outletsPower"
                  />
                </label>
              </div>
            </div>
          </fieldset>
          <fieldset
            class="w-full md:w-1/2 px-3 sm:px-0 md:px-3 my-2"
            v-else
          >
            <legend class="w-full text-center form-label">Cuartos</legend>
            <div
              v-for="(room, index) in rooms"
              v-bind:key="index"
              class="flex flex-wrap items-center md:-mx-3 mb-2"
            >
              <div class="w-full border-b border-1 mx-3 pb-2 form-label">Cuarto {{index+1}}</div>
              <div class="w-full md:flex-1 px-3 sm:px-0 md:px-3 mb-6 md:mb-2">
                <label class="block">
                  <span class="form-label">Ancho (m)</span>
                  <input
                    class="form-input mt-1 block w-full"
                    type="number"
                    min="0"
                    placeholder="Ancho"
                    v-model="room.width"
                  />
                </label>
              </div>
              <div class="w-full md:flex-1 px-3 sm:px-0 md:px-3 mb-3 md:mb-2">
                <label class="block">
                  <span class="form-label">Largo (m)</span>
                  <input
                    class="form-input mt-1 block w-full"
                    type="number"
                    min="0"
                    placeholder="Largo"
                    v-model="room.height"
                  />
                </label>
              </div>
              <div class="px-3 sm:px-0 md:px-3 pt-2 md:pt-5 items-center flex">
                <button
                  @click="removeRoom(index)"
                  type="button"
                  class="bg-red-500 hover:bg-red-400 mr-2 focus:outline-none focus:shadow-outline font-semibold text-white px-2 py-2 rounded-lg"
                >
                  <svg
                    class="fill-current w-5 h-5"
                    xmlns="http://www.w3.org/2000/svg"
                    viewBox="0 0 24 24"
                    width="24"
                    height="24"
                  >
                    <path
                      class="heroicon-ui"
                      d="M8 6V4c0-1.1.9-2 2-2h4a2 2 0 0 1 2 2v2h5a1 1 0 0 1 0 2h-1v12a2 2 0 0 1-2 2H6a2 2 0 0 1-2-2V8H3a1 1 0 1 1 0-2h5zM6 8v12h12V8H6zm8-2V4h-4v2h4zm-4 4a1 1 0 0 1 1 1v6a1 1 0 0 1-2 0v-6a1 1 0 0 1 1-1zm4 0a1 1 0 0 1 1 1v6a1 1 0 0 1-2 0v-6a1 1 0 0 1 1-1z"
                    />
                  </svg>
                </button>
                <button
                  v-if="index + 1 === rooms.length"
                  @click="addRoom"
                  type="button"
                  class="block bg-indigo-500 hover:bg-indigo-400 focus:outline-none focus:shadow-outline font-semibold text-white px-2 py-2 rounded-lg"
                >
                  <svg
                    class="fill-current w-5 h-5"
                    xmlns="http://www.w3.org/2000/svg"
                    viewBox="0 0 24 24"
                    width="24"
                    height="24"
                  >
                    <path d="M17 11a1 1 0 0 1 0 2h-4v4a1 1 0 0 1-2 0v-4H7a1 1 0 0 1 0-2h4V7a1 1 0 0 1 2 0v4h4z" />
                  </svg>
                </button>
              </div>
            </div>
          </fieldset>
          <fieldset class="w-full md:w-1/2 px-3 sm:px-0 md:px-3 my-2">
            <legend class="w-full text-center form-label">Equipos instalados</legend>
            <div
              v-for="(equipment, index) in equipments"
              v-bind:key="index"
              class="flex flex-wrap items-center md:-mx-3 mb-2"
            >
              <div class="w-full border-b border-1 mx-3 pb-2 form-label">Equipo {{index+1}}</div>
              <div class="w-full md:flex-1 px-3 sm:px-0 md:px-3 mb-6 md:mb-2">
                <label class="block">
                  <span class="form-label">Nombre </span>
                  <input
                    class="form-input mt-1 block w-full"
                    type="text"
                    placeholder="Nombre"
                    v-model="equipment.name"
                  />
                </label>
              </div>
              <div class="w-full md:flex-1 px-3 sm:px-0 md:px-3 mb-3 md:mb-2">
                <label class="block">
                  <span class="form-label">Cantidad (m)</span>
                  <input
                    class="form-input mt-1 block w-full"
                    type="number"
                    min="0"
                    placeholder="Cantidad"
                    v-model="equipment.quantity"
                  />
                </label>
              </div>
              <div class="w-full md:flex-1 px-3 sm:px-0 md:px-3 mb-3 md:mb-2">
                <label class="block">
                  <span class="form-label">Potencia (W)</span>
                  <input
                    class="form-input mt-1 block w-full"
                    type="number"
                    min="0"
                    placeholder="Potencia"
                    v-model="equipment.power"
                  />
                </label>
              </div>
              <div class="px-3 sm:px-0 md:px-3 pt-2 md:pt-5 items-center flex">
                <button
                  @click="removeEquipment(index)"
                  type="button"
                  class="bg-red-500 hover:bg-red-400 mr-2 focus:outline-none focus:shadow-outline font-semibold text-white px-2 py-2 rounded-lg"
                >
                  <svg
                    class="fill-current w-5 h-5"
                    xmlns="http://www.w3.org/2000/svg"
                    viewBox="0 0 24 24"
                    width="24"
                    height="24"
                  >
                    <path
                      class="heroicon-ui"
                      d="M8 6V4c0-1.1.9-2 2-2h4a2 2 0 0 1 2 2v2h5a1 1 0 0 1 0 2h-1v12a2 2 0 0 1-2 2H6a2 2 0 0 1-2-2V8H3a1 1 0 1 1 0-2h5zM6 8v12h12V8H6zm8-2V4h-4v2h4zm-4 4a1 1 0 0 1 1 1v6a1 1 0 0 1-2 0v-6a1 1 0 0 1 1-1zm4 0a1 1 0 0 1 1 1v6a1 1 0 0 1-2 0v-6a1 1 0 0 1 1-1z"
                    />
                  </svg>
                </button>
                <button
                  v-if="index + 1 === equipments.length"
                  @click="addEquipment"
                  type="button"
                  class="block bg-indigo-500 hover:bg-indigo-400 focus:outline-none focus:shadow-outline font-semibold text-white px-2 py-2 rounded-lg"
                >
                  <svg
                    class="fill-current w-5 h-5"
                    xmlns="http://www.w3.org/2000/svg"
                    viewBox="0 0 24 24"
                    width="24"
                    height="24"
                  >
                    <path d="M17 11a1 1 0 0 1 0 2h-4v4a1 1 0 0 1-2 0v-4H7a1 1 0 0 1 0-2h4V7a1 1 0 0 1 2 0v4h4z" />
                  </svg>
                </button>
              </div>
            </div>
          </fieldset>
        </div>
        <div class="sm:text-right mx-3 sm:mx-0 text-center mb-4">
          <button
            class="the-form__submit"
            type="submit"
          >Calcular</button>
        </div>
      </div>
    </form>

    <div class="the-result flex justify-center">
      <p class="the-result__content bg-teal-500 rounded-lg px-5 py-2 text-teal-900 inline-block">
        Potencia instalada en iluminación + tomacorrientes: P <sub>Inst I+T</sub> = {{installedPowerLightningOutlets}}W<br>
        Demanda máxima de iluminación + tomacorrientes: D <sub>Max I+T</sub> = {{maximumDemandLightningOutlets}}W<br>
        Potencia instalada en fuerza: P<sub>Inst F</sub> = {{installedForcePower}}W<br>
        Demanda máxima de fuerza: D<sub>Max F</sub> = {{maximumDemandForce}}W<br>
        Demanda máxima: D<sub>Max</sub> = {{maximumDemandForce + maximumDemandLightningOutlets}}
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
      rooms: [
        {
          width: 0,
          height: 0
        }
      ],
      equipments: [
        {
          name: 'Equipo 1',
          quantity: 1,
          power: 1000
        }
      ],
      canRemoveRoom: false,
      canRemoveEquipment: false,
      isGivenInstalledPower: false,
      maxSurface: 0,
      intakeLevel: "",
      chargeDensity: 0,
      lightningPower: 0,
      installedPowerLightningOutlets: 0,
      installedForcePower: 0,
      maximumDemandLightningOutlets: 0,
      maximumDemandForce: 0,
      outletsQuantity: 0,
      outletsPower: 0,
      lightingType: "flu", // fluorescente (flu) or incandescente (inc)
      table11: [
        { levelE: "Mínimo", levelT: "min", maxDemand: 3000, maxSurface: 80 },
        { levelE: "Medio", levelT: "mid", maxDemand: 7000, maxSurface: 140 },
        { levelE: "Elevado", levelT: "hig", maxDemand: 7001, maxSurface: 141 }
      ],
      table12: [
        { levelE: "Mínimo", levelT: "min", lighting: 10, fluorLighting: 6 },
        { levelE: "Medio", levelT: "mid", lighting: 15, fluorLighting: 8 },
        { levelE: "Elevado", levelT: "hig", lighting: 20, fluorLighting: 10 }
      ],
      table13: [
        { installedPowerMin: 0, installedPowerMax: 3000, demandFactor: 100 },
        { installedPowerMin: 3001, installedPowerMax: 8000, demandFactor: 35 },
        {
          installedPowerMin: 8001,
          installedPowerMax: 1000000,
          demandFactor: 25
        }
      ],
      table14: [
        { equipmentNumberMin: 0, equipmentNumberMax: 2, demandFactor: 100 },
        { equipmentNumberMin: 3, equipmentNumberMax: 5, demandFactor: 75 },
        { equipmentNumberMin: 6, equipmentNumberMax: 1000, demandFactor: 50 }
      ]
      // table15: [
      //   { apartmentsNumberMin: 2, apartmentsNumberMax: 4, minAvgLevel: 1.0, highLevel: 0.8 },
      //   { apartmentsNumberMin: 5, apartmentsNumberMax: 10, minAvgLevel: 0.8, highLevel: 0.7 },
      //   { apartmentsNumberMin: 11, apartmentsNumberMax: 20, minAvgLevel: 0.6, highLevel: 0.5 },
      //   { apartmentsNumberMin: 21, apartmentsNumberMax: 30, minAvgLevel: 0.4, highLevel: 0.3 },
      // ]
    };
  },
  watch: {
    rooms () {
      this.canRemoveRoom = this.rooms.length > 1;
    },
    equipments () {
      this.canRemoveEquipment = this.equipments.length > 1;
    }
  },
  methods: {
    calculate () {
      // Calcular el nivel de consumo
      this.intakeLevel = this.getIntakeLevel();
      // Calcular la densidad de carga
      this.chargeDensity = this.getChargeDensity();
      // Si se da la potencia instalada en iluminación y tomacorrientes entonces no se calcula
      // Asignar los valores a cero para que al realizar el cálculo se inicie desde cero
      if (!this.isGivenInstalledPower) {
        this.outletsQuantity = 0;
        this.lightningPower = 0;
        // Calcular la potencia instalada en iluminación y el número de tomas por cuarto
        this.rooms.forEach(room => {
          this.lightningPower += this.calculateLightningPerRoom(parseInt(room.width), parseInt(room.height));
          this.outletsQuantity += this.calculateOutletsPerRoom(parseInt(room.width), parseInt(room.height));
        });
        // A cada toma se atribuirá una potencia de 200 W para efectos de cálculo de cantidad como de potencia
        this.outletsPower = this.outletsQuantity * 200;
      }

      // // Potencia instalada en iluminación y tomacorrientes
      this.installedPowerLightningOutlets = parseInt(this.outletsPower) + parseInt(this.lightningPower);
      // Demanda máxima en iluminación y tomacorrientes
      this.maximumDemandLightningOutlets = this.calculateMaximumDemandLightningOutlets();
      // Potencia instalada en fuerza
      this.installedForcePower = this.getEquipmentsPower();
      // Demanda máxima en fuerza
      this.maximumDemandForce = this.calculateInstalledForcePower();
    },
    // Nivel de consumo
    getIntakeLevel () {
      if (this.maxSurface <= 80) {
        return this.table11[0];
      } else if (this.maxSurface <= 140) {
        return this.table11[1];
      } else {
        return this.table11[2];
      }
    },
    // Factor de demanda para iluminación y tomacorriente
    calculateMaximumDemandLightningOutlets () {
      let total = this.installedPowerLightningOutlets;
      let fd = 0;
      if (total <= 3000) {
        fd = total;
      } else if (total <= 8000 && total >= 3001) {
        fd = 3000 + (total - 3000) * 0.35;
      } else if (total >= 8001) {
        fd = 3000 + 5000 * 0.35 + (total - 8000) * 0.25;
      }
      return fd;
    },
    // Factor de demanda para tomas de fuerza
    calculateInstalledForcePower () {
      let equipmentNumber = this.getEquipmentsQuantity();
      let fc = 0;

      if (equipmentNumber <= 2) {
        fc = this.installedForcePower;
      } else if (equipmentNumber >= 3 && equipmentNumber <= 5) {
        fc = this.installedForcePower * 0.75;
      } else if (equipmentNumber >= 6) {
        fc = this.installedForcePower * 0.5;
      }
      return fc;
    },
    getEquipmentsPower () {
      let total = 0;
      this.equipments.forEach(equipment => {
        total += equipment.quantity * equipment.power;
      });
      return total;
    },
    getEquipmentsQuantity () {
      let total = 0;
      this.equipments.forEach(equipment => {
        total += parseInt(equipment.quantity);
      });
      return total;
    },
    calculateLightningPerRoom (width, height) {
      let p = (width * height) * this.chargeDensity;
      return this.lightingType == "flu" ? p * 1.8 : p;
    },
    calculateOutletsPerRoom (width, height) {
      let outletsQuantityPerRoom;
      let area = width * height;
      let perimeter = (width * 2) + (height * 2);
      if (area <= 10) outletsQuantityPerRoom = 1;
      else if (area > 10) {
        let outletsQuantityArea = Math.ceil(area / 10);
        let outletsQuantityPerimeter = Math.ceil(perimeter / 5);
        outletsQuantityPerRoom = (outletsQuantityArea > outletsQuantityPerimeter) ?
          outletsQuantityArea :
          outletsQuantityPerimeter;
      }
      return outletsQuantityPerRoom;
    },
    getChargeDensity () {
      let filteredTable = this.table12.filter(t => t.levelT == this.intakeLevel.levelT)[0];

      return this.lightingType == "flu" ?
        filteredTable.fluorLighting :
        filteredTable.lighting;
    },
    setGivenInstalledPower (isGiven) {
      this.isGivenInstalledPower = isGiven;
    },
    addRoom () {
      let checkEmptyrooms = this.rooms.filter(
        room => room.width === 0 || room.height === 0
      );
      if (checkEmptyrooms.length >= 1 && this.rooms.length > 0) {
        this.showMessage(
          "No se puede agregar un nuevo cuarto sin haber rellenado el actual."
        );
        return;
      }
      this.rooms.push({
        width: 0,
        height: 0
      });
    },
    removeRoom (roomId) {
      if (this.canRemoveRoom) this.rooms.splice(roomId, 1);
    },
    addEquipment () {
      let checkEmptyEquipments = this.equipments.filter(
        equipment => equipment.name === "" || equipment.quantity === 0 || equipment.power === 0
      );
      if (checkEmptyEquipments.length >= 1 && this.equipments.length > 0) {
        this.showMessage(
          "No se puede agregar un nuevo equipo sin haber rellenado el actual."
        );
        return;
      }
      this.equipments.push({
        name: 'Equipo nuevo',
        quantity: 1,
        power: 0,
      });
    },
    removeEquipment (equipmentId) {
      if (this.canRemoveEquipment) this.equipments.splice(equipmentId, 1);
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
        title: "¿Buscando ayuda?",
        html: `<p class="text-left">Si desea conocer la demanda máxima, tenga presente:<br>
              - ¿Cuál es la superficie máxima del lugar?<br>
              - ¿Cuáles son las diferentes habitaciones o lugares que hay?<br><br>
              Si ya dispone de los valores de pontencia instalada en iluminación y tomacorrientes, entonces solamente ingreselos.<br>
              Con estos datos, el programa le mostrará:<br>
              - <i>P Inst I+T</i>: Potencia instalada en iluminación + tomacorrientes<br>
              - <i>D Max I+T</i>: Demanda máxima de iluminación + tomacorrientes<br>
              - <i>PInst F</i>: Potencia instalada en fuerza<br>
              - <i>DMax F</i>: Demanda máxima de fuerza: <br>
              - <i>DMax</i>: Demanda máxima: <br>`,
        confirmButtonText: "¡Entendido!"
      });
    }
  }
};
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
      justify-content: space-between;
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