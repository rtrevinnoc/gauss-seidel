<template>
  <table>
    <tr v-for="m in eqNumber" v-bind:key="m">
      <td v-for="n in eqNumber" v-bind:key="n">
        <input
          type="text"
          :id="[m, n]"
          :name="[m, n]"
          v-model="eqCoefficients[m + ',' + n]"
          style="width: 40px"
        />x<sub>{{ n }}</sub> <span v-if="n != eqNumber">+</span>
      </td>
      <td>
        =
        <input
          type="text"
          :id="m"
          :name="m"
          v-model="eqResults[m]"
          style="width: 40px"
        />
      </td>
    </tr>
  </table>
  <button @click="eqNumber++" class="btn btn-primary" style="margin: 5px">
    Agregar ecuación
  </button>
  <button @click="eqNumber--" class="btn btn-danger" style="margin: 5px">
    Eliminar ecuación
  </button>
  <button @click="solve()" class="btn btn-success" style="margin: 5px">
    Resolver sistema de ecuaciones
  </button>
  <ul v-if="solArray != []">
    <li v-for="n in eqNumber" v-bind:key="n">
      x<sub>{{ n }}</sub> = {{ solArray[n - 1] }}
    </li>
  </ul>
</template>

<script>
export default {
  name: "App",
  components: {},
  data() {
    return {
      eqNumber: 3,
      eqCoefficients: {},
      eqResults: {},
      solArray: [],
    };
  },
  methods: {
    // seidel(coeficientes, soluciones, resultados)
    seidel(a, x, b) {
      // dimension de la matriz
      const n = a.length;

      // recorren las columnas
      for (let j = 0; j < n; j++) {
        // una variable temporal para los resultados
        var d = b[j];

        // recorres las filas
        for (let i = 0; i < n; i++) {
          if (j !== i) {
            // si es la variable actual, saltarlo
            d -= a[j][i] * x[i]; // restar la multiplicacion del coeficiente por el valor actual de la variable
          }
        }

        x[j] = d / a[j][j]; // actualizar la solucion dividida por el coeficiente de la variable actualizar
      }

      return x;
    },
    solve() {
      var solArray = new Array(this.eqNumber).fill(0);
      var errorArray = new Array(this.eqNumber);
      var prevSolArray = new Array(this.eqNumber).fill(0);
      var avgError = 100;
      const eqArray = new Array(this.eqNumber)
        .fill(0)
        .map(() => new Array(this.eqNumber).fill(0));
      const resArray = Object.values(this.eqResults).map(Number);

      for (const [key, value] of Object.entries(this.eqCoefficients)) {
        const index = key.split(",");
        eqArray[parseFloat(index[0]) - 1][parseFloat(index[1]) - 1] =
          parseFloat(value);
      }

      while (avgError > 0) {
        prevSolArray = [...solArray];
        solArray = this.seidel(eqArray, solArray, resArray);

        errorArray = solArray.map(
          (v, i) => (Math.abs(v - prevSolArray[i]) / v) * 100
        );
        avgError = errorArray.reduce((p, c, _, a) => p + c / a.length, 0);
      }
      console.log(solArray);
      this.solArray = solArray;
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

ul {
  list-style: none;
}
</style>
