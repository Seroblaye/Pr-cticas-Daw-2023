<template>
  <div>

    <label for="busqueda">Introduce un número para filtrar: </label>
    <input id="busqueda" v-model="searchText"  placeholder="Buscar...">
    <br>

    <vuetable ref="vuetable"
    v-if="filteredData.length > 0"
      :data="filteredData"
      :fields="fields"
      :api-mode="false"
    ></vuetable>
    
    <div id="resultado" v-else>
      <br>
      No hay datos que incluyan la busqueda
    </div>

    <br>
    <button @click="descargarCSV()">
        Descargar CSV
    </button>

  </div>
  </template>
  
  <script>
  import Vuetable from 'vuetable-2'
  import exportFromJSON from "export-from-json"
  
  export default {
    name: 'TablaAfluencia',
    components: {
      Vuetable
    },
    data() {
      return {
        fields: ['id', 'afluencia', 'comparacion' ],
        serch:'',
        data: {
          "data": [
            {
              "id": 1,
              "afluencia": 1000,
              "comparacion": 50
            },
            {
              "id": 2,
              "afluencia": 500,
              "comparacion": 25
            },
            {
              "id": 3,
              "afluencia": 750,
              "comparacion": 35
            },
            {
              "id": 4,
              "afluencia": 1200,
              "comparacion": 60
            },
            {
              "id": 5,
              "afluencia": 900,
              "comparacion": 1000
            },
            {
              "id": 6,
              "afluencia": 1500,
              "comparacion": 75
            },
            {
              "id": 7,
              "afluencia": 800,
              "comparacion": 40
            },
            {
              "id": 8,
              "afluencia": 1100,
              "comparacion": 1500
            },
            {
              "id": 9,
              "afluencia": 650,
              "comparacion": 30
            },
            {
              "id": 10,
              "afluencia": 950,
              "comparacion": 1000
            }
          ]
        },
        searchText: '',
      }
    }, 
    
    computed: {
        filteredData() {
          const busqueda = this.searchText;
            return this.data.data.filter(obj => {
              return obj.afluencia.toString() === busqueda  || obj.id.toString() === busqueda || obj.comparacion.toString().includes(busqueda);
            });
          },
        },

      methods: {
        ordenarAfluencia() {
            this.data.data.sort((a, b) => a.afluencia - b.afluencia);
        },

        operacionComparacion() {
            this.data.data = this.data.data.map(obj => {
              const operacion = ((obj.comparacion * 100) / obj.afluencia - 100).toFixed(2)
              const color = operacion < 0 ? 'red' : 'green'
              const comparacion = `${obj.comparacion} | <span style="color:${color}">${operacion}%</span>`
              return {
                ...obj,
                comparacion
              }
          });
        },

        descargarCSV(){
          const data = this.data.data.map(obj => {
                return {
                  ...obj,
                  comparacion: obj.comparacion.replace(/<[^>]*>/g, '')
                }
              });

          const nombreArchivo = 'Trabajo';
          const exportType = exportFromJSON.types.csv;
          exportFromJSON({data, fileName: nombreArchivo, exportType});
          
        }
      },

      created() {
        this.ordenarAfluencia();
        this.operacionComparacion();
        
    }
  }
</script>
