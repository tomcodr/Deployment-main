<template>
  <b class="drehzahl-value">{{ rpmValue !== null ? rpmValue : 'N/A' }}</b>
</template>

<script>
export default {
  data() {
    return {
      rpmValue: null,
    };
  },
  methods: {
    async fetchrpmValue() {
      try {
        const response = await fetch("https://cartrackerapi.onrender.com/api/v1/fahrzeuge/67c012ef-39f7-48c1-8d7a-092fcad45c08/messwerte");
        const data = await response.json();

        console.log("API Response:", data);

        if (Array.isArray(data.messwerte) && data.messwerte.length > 0) {
          const rpm = data.messwerte[0]?.rpm;

          if (rpm !== undefined && rpm !== null) {
            const roundedRpm = Math.round(rpm);
            this.rpmValue = roundedRpm.toString();
          } else {
            console.error("Invalid or missing value for rpm:", rpm);
            this.rpmValue = null;
          }
        } else {
          console.error("Invalid or empty data for rpm data:", data.messwerte);
          this.rpmValue = null;
        }
      } catch (error) {
        console.error("Error loading rpm data:", error);
        this.rpmValue = null;
      }
    },
  },
  mounted() {
    // Set the update interval every 5 seconds (5000 milliseconds)

    // Perform fetchData once during initialization
    this.fetchrpmValue();
  },
};
</script>
