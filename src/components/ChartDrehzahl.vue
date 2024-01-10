<template>
  <div class="chartBox">
    <canvas id="myChart"></canvas>
  </div>
</template>

<script>
export default {
  mounted() {
    const data = {
      labels: [],
      datasets: [{
        label: 'RPM',
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
              onRefresh: chart => {
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
          },
          y: {
            beginAtZero: true
          }
        }
      }
    };

    const myChart = new Chart(
      document.getElementById('myChart'),
      config
    );
  }
};
</script>

<style scoped>
.chartBox {
  width: 700px;
}
</style>
