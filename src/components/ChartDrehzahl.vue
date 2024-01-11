<template>
  <div class="chartBox">
    <canvas id="myChart"></canvas>
  </div>
</template>

<script>


export default {
  data() {
    return {
      chart: null,
      liveMode: true,
    };
  },
  mounted() {
    this.setupChart();
  },
  methods: {
    setupChart() {
      const data = {
        labels: [],
        datasets: [{
          label: 'RPM - Live Mode',
          data: [],
          backgroundColor: 'rgba(75, 192, 192, 1)',
          borderColor: 'rgba(75, 192, 192, 1)',
          borderWidth: 1
        }]
      };

      const config = {
        type: 'line',
        data,
        options: {
          scales: {
            x: {
              type: 'realtime',
              realtime: {
                delay: 2000,
                duration: 20000,
                onRefresh: chart => {
                  if (this.liveMode) {
                    // Fetch data from API for RPM
                    fetch('https://cartrackerapi.onrender.com/api/v1/fahrzeuge/67c012ef-39f7-48c1-8d7a-092fcad45c08/messwerte?type=rpm')
                      .then(response => response.json())
                      .then(apiData => {
                        // Add new data to the chart
                        chart.data.labels.push(new Date().toLocaleTimeString());
                        chart.data.datasets[0].data.push(apiData.messwerte[0].rpm);
                      });
                  }
                }
              }
            },
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
        }
      };

      this.chart = new Chart(
        document.getElementById('myChart'),
        config
      );
    },
    toggleLiveMode() {
      this.liveMode = !this.liveMode;
      if (this.liveMode) {
        // Resume real-time mode
        this.chart.options.scales.x.realtime.onRefresh(this.chart);
      }
    },
  },
};
</script>

<style scoped>
.chartBox {
  width: 550px;
}
</style>
