<template>
    <div class="relative m-2 ">
        <input v-model="searchTerm" type="text" id="search" class="mt-4 block pb-2.5 pt-4 w-full text-sm text-green-900 bg-transparent rounded-lg border-2 border-gray-300 appearance-none dark:text-white dark:border-green-600 dark:focus:border-blue-500 focus:outline-none focus:ring-0 focus:border-blue-600 peer" placeholder=" " />
        <label for="search" class="absolute text-sm text-blue-500 dark:text-blue-400 duration-300 transform -translate-y-4 scale-75 top-2 z-10 origin-[0] bg-white dark:bg-blue-900 px-2 peer-focus:px-2 peer-focus:text-blue-600 peer-focus:dark:text-blue-500 peer-placeholder-shown:scale-100 peer-placeholder-shown:-translate-y-1/2 peer-placeholder-shown:top-1/2 peer-focus:top-2 peer-focus:scale-75 peer-focus:-translate-y-4 left-1">Was möchten Sie suchen?</label>
    </div>
    <table-lite class="mb-5"
      :is-static-mode="true"
      :columns="table.columns"
      :rows="table.rows"
      :total="table.totalRecordCount"
      :sortable="table.sortable"
      :messages="table.messages"
    ></table-lite>
  </template>
  
  <script>
  import { defineComponent, reactive, ref, computed } from "vue";
  import TableLite from "vue3-table-lite";
  import jsonData from '../assets/data.json';
  export default defineComponent({
    name: "Table",
    components: { TableLite },
    setup() {
      const searchTerm = ref(""); // Suchbegriff
      //Daten reaktiv
      const data = reactive([]);
      jsonData.forEach(jsdt => {
        data.push({
		//Tabelleninhalte bestimmen
            id: jsdt.id,
            land: jsdt.land,
            unternehmen: jsdt.unternehmen,
		kontinent: jsdt.kontinent,
            co2: jsdt.co2.replace(",", ""),
        })
      })

      // Tabellen Konfiguration
      const table = reactive({
        columns: [
          {
            label: "Nr",
            field: "id",
            width: "1%",
            sortable: true,
            isKey: true,
          },
		   {
            label: "Unternehmen",
            field: "unternehmen",
            width: "3%",
            sortable: true,
          },
  {
            label: "Land",
            field: "land",
            width: "3%",
            sortable: true,
          },
          {
            label: "Kontinent",
            field: "kontinent",
            width: "3%",
            sortable: true,
          },
          {
            label: "CO²-Emissionen in Tonnen (1965-2017)",
            field: "co2",
            width: "3%",
            sortable: true,
          },
        ],
        rows: computed(() => {
          return data.filter(
            (x) =>
              x.unternehmen.toLowerCase().includes(searchTerm.value.toLowerCase()) ||
              x.land.toLowerCase().includes(searchTerm.value.toLowerCase())
          );
        }),
        totalRecordCount: computed(() => {
          return table.rows.length;
        }),
        sortable: {
          order: "id",
          sort: "asc",
        },
        messages: {
            pagingInfo: "Zeige {0} bis {1} von {2}",
            pageSizeChangeLabel: "Zeilen pro Seite: ",
            gotoPageLabel: " Weiter zu Seite: ",
            noDataAvailable: "Keine Daten verfügbar",
        },
      });
      return {
        searchTerm,
        table,
      };
    },
  });
  </script>
