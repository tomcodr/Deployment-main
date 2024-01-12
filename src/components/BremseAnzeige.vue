<template>
  <div class="brake-container">
    <b class="brake-value">{{ brakeInputValue !== null ? brakeInputValue : 'N/A' }}</b>
  </div>
</template>

<script>
export default {
  data() {
    return {
      brakeInputValue: null,
    };
  },
  methods: {
    async fetchData() {
      try {
        const response = await fetch("https://cartrackerapi.onrender.com/api/v1/fahrzeuge/1/messwerte");
        const data = await response.json();

        if (data.messwerte && data.messwerte.length > 0) {
          const brakeInput = data.messwerte.find(item => item.hasOwnProperty('brake_input'))?.brake_input;
          if (brakeInput !== undefined && brakeInput !== null) {
            // Runde auf 3 Nachkommastellen und konvertiere zu String
            this.brakeInputValue = brakeInput.toFixed(3).toString();
          } else {
            console.error("Ungültiger oder fehlender Wert für brake_input:", brakeInput);
            // Setze brakeInputValue auf null, um "N/A" anzuzeigen
            this.brakeInputValue = null;
          }
        } else {
          console.error("Leere oder ungültige Daten für brake_input:", data.messwerte);
          // Setze brakeInputValue auf null, um "N/A" anzuzeigen
          this.brakeInputValue = null;
        }
      } catch (error) {
        console.error("Fehler beim Laden der Messwerte:", error);
        // Setze brakeInputValue auf null, um "N/A" anzuzeigen
        this.brakeInputValue = null;
      }
    },
  },
  mounted() {
    this.fetchData();
  },
};
</script>