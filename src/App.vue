<template>
    <table>
        <tr v-for="m in eqNumber" v-bind:key="m">
            <td v-for="n in eqNumber" v-bind:key="n"><input type="text" :id="[m, n]" :name="[m, n]" v-model="eqCoefficients[m + ',' + n]" style="width: 20px;">x<sub>{{ n }}</sub> <span v-if="n != eqNumber">+</span></td>
            <td> = <input type="text" :id="m" :name="m" v-model="eqResults[m]" style="width: 20px;"></td>
        </tr>
    </table>
    <button @click="eqNumber++">Agregar ecuación</button>
    <button @click="eqNumber--">Eliminar ecuación</button>
    <button @click="solve()">Resolver sistema de ecuaciones</button>
    <ul v-if="solArray != []">
        <li v-for="n in eqNumber" v-bind:key="n">x<sub>{{ n }}</sub> = {{ solArray[n - 1] }}</li>
    </ul>
</template>

<script>
export default {
    name: 'App',
    components: {
    },
    data() {
        return {
            eqNumber: 3,
            eqCoefficients: {},
            eqResults: {},
            solArray: [],
        }
    },
    methods: {
        seidel(a, x, b) {
            const n = a.length

            for (let j = 0; j < n; j++) {
                var d = b[j]

                for (let i = 0; i < n; i++) {
                    if (j !== i) {
                        d -= a[j][i] * x[i]
                    }
                }

                x[j] = d / a[j][j]
            }

            return x
        },
        solve() {
            var solArray = new Array(this.eqNumber).fill(0)
            var errorArray = new Array(this.eqNumber)
            var prevSolArray = new Array(this.eqNumber).fill(0)
            var avgError = 100;
            const eqArray = new Array(this.eqNumber).fill(0).map(() => new Array(this.eqNumber).fill(0))
            const resArray = Object.values(this.eqResults).map(Number);

            for (const [key, value] of Object.entries(this.eqCoefficients)) {
                const index = key.split(",")
                eqArray[parseFloat(index[0]) - 1][parseFloat(index[1]) - 1] = parseFloat(value);
            }

            while (avgError > 0) {
                prevSolArray = [...solArray]
                solArray = this.seidel(eqArray, solArray, resArray)

                errorArray = solArray.map((v, i) => (Math.abs(v - prevSolArray[i])/ v) * 100);
                avgError = errorArray.reduce((p,c,_,a) => p + c/a.length,0)
            }
            console.log(solArray)
            this.solArray = solArray;
        }
    }
}
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
