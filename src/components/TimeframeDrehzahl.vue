<template>
  <div>
    <div class="chartMenu">
      <p>WWW.CHARTJS3.COM (Chart JS {{ chartVersion }})</p>
    </div>
    <div class="chartCard">
      <div class="chartBox">
        <canvas ref="myChart"></canvas>
        <label for="datetimeStart">Startzeit:</label>
        <input @change="filterData" type="datetime-local" id="datetimeStart" v-model="datetimeStart" step="1">
        <label for="datetimeEnd">Endzeit:</label>
        <input @change="filterData" type="datetime-local" id="datetimeEnd" v-model="datetimeEnd" step="1">
      </div>
    </div>
  </div>
</template>

<script>
import { Chart } from 'chart.js/auto';

export default {
  data() {
    return {
      datetimeStart: '2023-12-10T16:48:50',
      datetimeEnd: '2024-01-10T16:48:50',
      chartVersion: Chart.version,
      myChart: null,
      chartData: {
        labels: [],
        datasets: [{
          label: 'RPM Values',
          data: [],
          backgroundColor: 'rgba(54, 162, 235, 0.2)',
          borderColor: 'rgba(54, 162, 235, 1)',
          borderWidth: 1
        }]
      },
    };
  },
  methods: {
    async fetchData() {
      const apiEndpoint = 'https://cartrackerapi.onrender.com/api/v1/fahrzeuge/67c012ef-39f7-48c1-8d7a-092fcad45c08/messwerte';
      const queryParams = `von=${encodeURIComponent(this.datetimeStart)}&bis=${encodeURIComponent(this.datetimeEnd)}&type=rpm`;
      const url = `${apiEndpoint}?${queryParams}`;
      console.log(url); // Log the constructed URL
      try {
        const response = await fetch(url);

        if (!response.ok) {
          throw new Error(`Failed to fetch data. Status: ${response.status}`);
        }

        const data = await response.json();

        if (data.message === "Data not found") {
          console.warn("No data found for the specified date range.");
          return;
        }

        this.chartData.labels = data.messwerte.map(entry => entry.timestamp);
        this.chartData.datasets[0].data = data.messwerte.map(entry => entry.rpm);

        // Update Chart.js data
        this.myChart.data = this.chartData;

        this.myChart.update();
      } catch (error) {
        console.error('Error fetching data:', error);
      }
    },
    filterData() {
      this.fetchData();
    },
  },
  mounted() {
    this.myChart = new Chart(this.$refs.myChart, {
      type: 'line',
      data: this.chartData,
      options: {
        scales: {
          y: {
            beginAtZero: true
          }
        },
        plugins: {
          zoom: {
            zoom: {
              wheel: {
                enabled: true,
              },
              pinch: {
                enabled: true,
              },
              mode: 'x',
            },
            pan: {
              enabled: true,
              mode: 'x',
            },
          },
        },
      },
    });
    
    // Initial data fetch and chart render
    this.fetchData();
  },
};
</script>


<style scoped>
* {
  margin: 0;
  padding: 0;
  font-family: sans-serif;
}

.chartMenu {
  width: 100vw;
  height: 40px;
  background: #1A1A1A;
  color: rgba(54, 162, 235, 1);
}

.chartMenu p {
  padding: 10px;
  font-size: 20px;
}


.chartCard {
  width: 100vw;
  height: calc(100vh - 40px);
  background: rgba(54, 162, 235, 0.2);
  display: flex;
  align-items: center;
  justify-content: center;
}

.chartBox {
  width: 700px;
  padding: 20px;
  border-radius: 20px;
  border: solid 3px rgba(54, 162, 235, 1);
  background: white;
}
</style>
