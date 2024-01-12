<template>

</template>

<script>
export default {
name:'CentralFetcher',

methods:{
    async fetchCarData() {
      try {
        const response = await fetch('https://cartrackerapi.onrender.com/api/v1/fahrzeuge/67c012ef-39f7-48c1-8d7a-092fcad45c08/messwerte');
        const data = await response.json();
        this.carData = data.messwerte[0];
      } catch (error) {
        console.error('Fehler beim Abrufen der Fahrzeugdaten:', error);
      }
    },
},


}
</script>

<style>

</style>