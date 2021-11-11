<template>
  <div class="home">
    <p class="title title-area">Населений пункт</p>
    <select class="areas" v-model="currentCity" @change="getWarehouses">
      <option :value="null">Выберете область</option>
      <option
        v-for="(area, index) in allAreas"
        :key="index"
        :value="area.AreasCenter"
      >
        {{ area.Description }}
      </option>
    </select>
    <label v-if="currentCity && warehouses.length > 0">
      <p class="title title-warehouse">Поштове вiддiлення</p>
      <select class="warehouses">
        <option
          v-for="(warehouse, index) in warehouses"
          :key="index"
          :value="warehouse.SettlementRef"
        >
          {{ warehouse.Description }}
        </option>
      </select>
    </label>
  </div>
</template>

<script>
import axios from "axios";

const key = "fd92916270aeaf36145d47e72f109342";

export default {
  name: "Home",
  props: {},
  data: function () {
    return {
      key: key,
      allAreas: [],
      warehouses: [],
      currentCity: null,
    };
  },
  mounted: function () {
    axios
      .post("https://api.novaposhta.ua/v2.0/json/", {
        apiKey: this.key,
        modelName: "Address",
        calledMethod: "getAreas",
        methodProperties: {},
      })
      .then(({ data }) => (this.allAreas = data.data));
  },
  methods: {
    getWarehouses: function () {
      if (this.currentCity) {
        axios
          .post("https://api.novaposhta.ua/v2.0/json/", {
            apiKey: this.key,
            modelName: "AddressGeneral",
            calledMethod: "getWarehouses",
            methodProperties: {
              CityRef: this.currentCity,
              FindByString: "Відділення",
            },
          })
          .then(({ data }) => (this.warehouses = data.data));
      }
    },
  },
};
</script>

<style scoped>
.home {
  display: flex;
  flex-direction: column;
}

.title {
  font-weight: 700;
  font-size: 20px;
  color: rgb(24, 39, 59);
}

select {
  width: 200px;
  padding: 10px;
  border: 2px solid rgb(13, 13, 34);
  border-radius: 5px;
  font-size: 15px;
  color: rgb(24, 39, 59);
}

.title-warehouse {
  margin-top: 30px;
}

.warehouses {
  width: 400px;
}

select:focus {
  outline: none;
}
</style>
