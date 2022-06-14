---
title: "SondeHub Stations Chart"
subtitle: "Creating an interactive Donut Chart using Chart.js"
date: 2022-05-18 00:00:00 -0400
categories: [SondeHub]
---

<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/swagger-ui/4.11.1/swagger-ui.css">
<script type="text/javascript" language="javascript" src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/3.7.1/chart.min.js"></script>
<script type="text/javascript" language="javascript" src="https://cdnjs.cloudflare.com/ajax/libs/swagger-ui/4.11.1/swagger-ui-bundle.js"></script>

This blog post contains instructions on how to use Chart.js to create a custom donut chart specifically designed for showing receiver data from SondeHub.

<h2>Chart.js</h2>

Chart.js is a simple JavaScript charting library for creating responsive visualisations of various types and styles.

Chart.js can easily be added on an existing website by including the latest version of the library from a CDN or locally.

```html
<script type="text/javascript" language="javascript" src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/3.7.1/chart.js"></script>
```

<h3>Donut Chart</h3>

To render a chart a canvas element needs to be added to the website</p>

```html
<canvas id="myChart" width="400" height="400"></canvas>
```

The graph requires a data object which contains the dataset and formatting options for the graph.</p>

```javascript
const data = {
   labels: [
      'Red',
      'Blue',
      'Yellow'
   ],
   datasets: [{
      data: [300, 50, 100],
      backgroundColor: [
         'rgb(255, 99, 132)',
         'rgb(54, 162, 235)',
         'rgb(255, 205, 86)'
      ]
   }]
};
```

The graph can now be created and rendered by passing through the canvas element and data object.

```javascript
const ctx = document.getElementById('myChart').getContext('2d');

const myChart = new Chart(ctx, {
   type: 'doughnut',
   data: data
});
```

<h3>Result</h3>

<canvas id="chart1" width="400" height="400"></canvas>

<script>
   const data1 = {
      labels: [
         'Red',
         'Blue',
         'Yellow'
      ],
      datasets: [{
         data: [300, 50, 100],
         backgroundColor: [
            'rgb(255, 99, 132)',
            'rgb(54, 162, 235)',
            'rgb(255, 205, 86)'
         ]
      }]
   };

   const ctx1 = document.getElementById('chart1').getContext('2d');

   const myChart1 = new Chart(ctx1, {
      type: 'doughnut',
      data: data1
   });
</script>

<h3>Custom Item Tooltips</h3>

The default tooltip only shows the label and value for each entry.

This can be overriden by creating a custom tooltip which can show the percentage for each entry.

```javascript
const myChart = new Chart(ctx, {
   type: 'doughnut',
   data: data,
   options: {
   plugins: {
      tooltip: {
         enabled: true,
         callbacks: {
            label: (ttItem) => {
               let sum = 0;
   
               let dataArr = ttItem.dataset.data;
               dataArr.map(data => {
                  sum += Number(data);
               });
   
               let percentage = (ttItem.parsed * 100 / sum).toFixed(2) + '%';
               return `${ttItem.parsed}: ${percentage}`;
            }
         }
      }
   }
   }
});
```

<h3>Result</h3>

<canvas id="chart2" width="400" height="400"></canvas>

<script>
   const data2 = {
      labels: [
         'Red',
         'Blue',
         'Yellow'
      ],
      datasets: [{
         data: [300, 50, 100],
         backgroundColor: [
            'rgb(255, 99, 132)',
            'rgb(54, 162, 235)',
            'rgb(255, 205, 86)'
         ]
      }]
   };

   const ctx2 = document.getElementById('chart2').getContext('2d');

   const myChart2 = new Chart(ctx2, {
      type: 'doughnut',
      data: data2,
      options: {
         plugins: {
            tooltip: {
            enabled: true,
            callbacks: {
               label: (ttItem) => {
                  let sum = 0;

                  let dataArr = ttItem.dataset.data;
                  dataArr.map(data => {
                  sum += Number(data);
                  });

                  let percentage = (ttItem.parsed * 100 / sum).toFixed(2) + '%';
                  return `${ttItem.parsed}: ${percentage}`;
               }
            }
            }
         }
      }
   });
</script>

<h3>Nested Donut Chart</h3>

Chart.js includes limited nested data support with donut charts by passing through multiple datasets.

```javascript
const data = {
   labels: [
      'Red',
      'Blue',
      'Yellow'
   ],
   datasets: [{
      data: [300, 50, 100],
      backgroundColor: [
         'rgb(255, 99, 132)',
         'rgb(54, 162, 235)',
         'rgb(255, 205, 86)'
      ]
   }, {
      data: [200, 75, 175],
      backgroundColor: [
         'rgb(255, 99, 132)',
         'rgb(54, 162, 235)',
         'rgb(255, 205, 86)'
      ]
   }]
};
```

<h3>Result</h3>

<canvas id="chart3" width="400" height="400"></canvas>

<script>
   const data3 = {
      labels: [
        'Red',
        'Blue',
        'Yellow'
      ],
      datasets: [{
        data: [300, 50, 100],
        backgroundColor: [
          'rgb(255, 99, 132)',
          'rgb(54, 162, 235)',
          'rgb(255, 205, 86)'
        ]
      }, {
        data: [200, 75, 175],
        backgroundColor: [
          'rgb(255, 99, 132)',
          'rgb(54, 162, 235)',
          'rgb(255, 205, 86)'
        ]
      }]
   };

   const ctx3 = document.getElementById('chart3').getContext('2d');

   const myChart3 = new Chart(ctx3, {
      type: 'doughnut',
      data: data3,
      options: {
         plugins: {
            tooltip: {
               enabled: true,
               callbacks: {
                  label: (ttItem) => {
                     let sum = 0;

                     let dataArr = ttItem.dataset.data;
                     dataArr.map(data => {
                     sum += Number(data);
                     });

                     let percentage = (ttItem.parsed * 100 / sum).toFixed(2) + '%';
                     return `${ttItem.parsed}: ${percentage}`;
                  }
               }
            }
         }
      }
   });
</script>

This works well when the inner dataset is the same length as the outer dataset however to have sub-categories in the inner-dataset a custom legend handler is required.

```javascript
datasets: [{
   data: [300, 50, 100],
   backgroundColor: [
      'rgb(255, 99, 132)',
      'rgb(54, 162, 235)',
      'rgb(255, 205, 86)'
   ]
}, {
   data: [150, 150, 50, 75, 25],
   backgroundColor: [
      'rgb(255, 99, 132)',
      'rgb(255, 99, 132)',
      'rgb(54, 162, 235)',
      'rgb(255, 205, 86)',
      'rgb(255, 205, 86)'
   ]
}]
```

The sub-entries can be grouped with their parents if their values are perfectly nested using the following custom legend handler.

```javascript
plugins: {
   legend: {
      labels: {
         generateLabels: chart => chart.data.labels.map((l, i) => ({
            text: l,
            index: i,
            fillStyle: chart.data.datasets[0].backgroundColor[i],
            strokeStyle: chart.data.datasets[0].backgroundColor[i],
            hidden: chart.getDatasetMeta(0).data[i].hidden
         })),
      },
      onClick: (event, legendItem, legend) => {
         var start = 0;
         var end = 0;
         var sum = 0;
         let chart = legend.chart;
         let hidden = !chart.getDatasetMeta(0).data[legendItem.index].hidden;
         chart.getDatasetMeta(0).data[legendItem.index].hidden = hidden;
         chart.data.datasets[0].data.forEach((v, i) => {
            var value = chart.getDatasetMeta(0).data[i].$context.parsed;
            if (i == legendItem.index) {
               start = sum;
               end = sum + value;
            }
            sum += value;
         });
         sum = 0;
         chart.data.datasets[1].data.forEach((v, i) => {
            var value = chart.getDatasetMeta(1).data[i].$context.parsed;
            sum += value;
            if (sum > start && sum <= end) {
               chart.getDatasetMeta(1).data[i].hidden = hidden;
            }
         });
         chart.update();
      }
   }
}
```

<h3>Result</h3>

<canvas id="chart4" width="400" height="400"></canvas>

<script>
   const data4 = {
      labels: [
         'Red',
         'Blue',
         'Yellow'
      ],
      datasets: [{
         data: [300, 50, 100],
         backgroundColor: [
            'rgb(255, 99, 132)',
            'rgb(54, 162, 235)',
            'rgb(255, 205, 86)'
         ]
      }, {
         data: [150, 150, 50, 75, 25],
         backgroundColor: [
            'rgb(255, 99, 132)',
            'rgb(255, 99, 132)',
            'rgb(54, 162, 235)',
            'rgb(255, 205, 86)',
            'rgb(255, 205, 86)'
         ]
      }]
   };

   const ctx4 = document.getElementById('chart4').getContext('2d');

   const myChart4 = new Chart(ctx4, {
      type: 'doughnut',
      data: data4,
      options: {
         plugins: {
            tooltip: {
               enabled: true,
               callbacks: {
                  label: (ttItem) => {
                     let sum = 0;

                     let dataArr = ttItem.dataset.data;
                     dataArr.map(data => {
                     sum += Number(data);
                     });

                     let percentage = (ttItem.parsed * 100 / sum).toFixed(2) + '%';
                     return `${ttItem.parsed}: ${percentage}`;
                  }
               }
            },
            legend: {
               labels: {
                  generateLabels: chart => chart.data.labels.map((l, i) => ({
                     text: l,
                     index: i,
                     fillStyle: chart.data.datasets[0].backgroundColor[i],
                     strokeStyle: chart.data.datasets[0].backgroundColor[i],
                     hidden: chart.getDatasetMeta(0).data[i].hidden
                  })),
               },
               onClick: (event, legendItem, legend) => {
                  var start = 0;
                  var end = 0;
                  var sum = 0;
                  let chart = legend.chart;
                  let hidden = !chart.getDatasetMeta(0).data[legendItem.index].hidden;
                  chart.getDatasetMeta(0).data[legendItem.index].hidden = hidden;
                  chart.data.datasets[0].data.forEach((v, i) => {
                     var value = chart.getDatasetMeta(0).data[i].$context.parsed;
                     if (i == legendItem.index) {
                        start = sum;
                        end = sum + value;
                     }
                     sum += value;
                  });
                  sum = 0;
                  chart.data.datasets[1].data.forEach((v, i) => {
                     var value = chart.getDatasetMeta(1).data[i].$context.parsed;
                     sum += value;
                     if (sum > start && sum <= end) {
                        chart.getDatasetMeta(1).data[i].hidden = hidden;
                     }
                  });
                  chart.update();
               }
            }
         }
      }
   });
</script>

<h3>Toggle Nested Data</h3>

The nested data can also be toggled on and off by creating a simple function.

```javascript
document.getElementById('nested').addEventListener('click', () => {
   if (data.datasets[1].hasOwnProperty("hidden") && data.datasets[1].hidden == true) {
      data.datasets[1].hidden = false;
   } else {
      data.datasets[1].hidden = true;
   }
   myChart.update();
});
```

This function will toggle the nested data visiblity when the following button is pressed.

```html
<button id="nested" class="button">Toggle Nested</button>
```

<h3>Result</h3>

<canvas id="chart5" width="400" height="400"></canvas>

<button id="nested5" class="button">Toggle Nested</button>

<script>
   const data5 = {
      labels: [
         'Red',
         'Blue',
         'Yellow'
      ],
      datasets: [{
         data: [300, 50, 100],
         backgroundColor: [
            'rgb(255, 99, 132)',
            'rgb(54, 162, 235)',
            'rgb(255, 205, 86)'
         ]
      }, {
         data: [150, 150, 50, 75, 25],
         backgroundColor: [
            'rgb(255, 99, 132)',
            'rgb(255, 99, 132)',
            'rgb(54, 162, 235)',
            'rgb(255, 205, 86)',
            'rgb(255, 205, 86)'
         ]
      }]
   };

   const ctx5 = document.getElementById('chart5').getContext('2d');

   const myChart5 = new Chart(ctx5, {
      type: 'doughnut',
      data: data5,
      options: {
         plugins: {
            tooltip: {
               enabled: true,
               callbacks: {
                  label: (ttItem) => {
                     let sum = 0;

                     let dataArr = ttItem.dataset.data;
                     dataArr.map(data => {
                     sum += Number(data);
                     });

                     let percentage = (ttItem.parsed * 100 / sum).toFixed(2) + '%';
                     return `${ttItem.parsed}: ${percentage}`;
                  }
               }
            },
            legend: {
               labels: {
                  generateLabels: chart => chart.data.labels.map((l, i) => ({
                     text: l,
                     index: i,
                     fillStyle: chart.data.datasets[0].backgroundColor[i],
                     strokeStyle: chart.data.datasets[0].backgroundColor[i],
                     hidden: chart.getDatasetMeta(0).data[i].hidden
                  })),
               },
               onClick: (event, legendItem, legend) => {
                  var start = 0;
                  var end = 0;
                  var sum = 0;
                  let chart = legend.chart;
                  let hidden = !chart.getDatasetMeta(0).data[legendItem.index].hidden;
                  chart.getDatasetMeta(0).data[legendItem.index].hidden = hidden;
                  chart.data.datasets[0].data.forEach((v, i) => {
                     var value = chart.getDatasetMeta(0).data[i].$context.parsed;
                     if (i == legendItem.index) {
                        start = sum;
                        end = sum + value;
                     }
                     sum += value;
                  });
                  sum = 0;
                  chart.data.datasets[1].data.forEach((v, i) => {
                     var value = chart.getDatasetMeta(1).data[i].$context.parsed;
                     sum += value;
                     if (sum > start && sum <= end) {
                     chart.getDatasetMeta(1).data[i].hidden = hidden;
                     }
                  });
                  chart.update();
               }
            }
         }
      }
   });

   document.getElementById('nested5').addEventListener('click', () => {
      if (data5.datasets[1].hasOwnProperty("hidden") && data5.datasets[1].hidden == true) {
         data5.datasets[1].hidden = false;
      } else {
         data5.datasets[1].hidden = true;
      }
      myChart5.update();
   });
</script>

<h3>Switch datasets</h3>

To show multiple datasets on the graph switching functionality has to be implemented.

The first step is to move the datasets to a new object.

```javascript
const datasets = [
   [{
   data: [300, 50, 100],
   backgroundColor: [
      'rgb(255, 99, 132)',
      'rgb(54, 162, 235)',
      'rgb(255, 205, 86)'
   ]
   }, {
   data: [150, 150, 50, 75, 25],
   backgroundColor: [
      'rgb(255, 99, 132)',
      'rgb(255, 99, 132)',
      'rgb(54, 162, 235)',
      'rgb(255, 205, 86)',
      'rgb(255, 205, 86)'
   ]
   }],
   [{
   data: [150, 100, 200],
   backgroundColor: [
      'rgb(255, 99, 132)',
      'rgb(54, 162, 235)',
      'rgb(255, 205, 86)'
   ]
   }, {
   data: [150, 25, 75, 100, 50, 50],
   backgroundColor: [
      'rgb(255, 99, 132)',
      'rgb(54, 162, 235)',
      'rgb(54, 162, 235)',
      'rgb(255, 205, 86)',
      'rgb(255, 205, 86)',
      'rgb(255, 205, 86)'
   ]
   }]
]
```

The data object then needs to point to the first entry in datasets.

```javascript
const data = {
   labels: [
      'Red',
      'Blue',
      'Yellow'
   ],
   datasets: datasets[0]
};
```

To switch between these datasets a new function is required.

```javascript
document.getElementById('dataset').addEventListener('click', () => {
   if (datasets.indexOf(data.datasets) == -1 || datasets.indexOf(data.datasets) == 1) {
      data.datasets = datasets[0]
   } else {
      data.datasets = datasets[1]
   }
   myChart.update();
});
```

This function will switch the visible dataset when the following button is pressed.

```html
<button id="dataset" class="button">Switch Dataset</button>
```

The toggle nested function will need to be updated so that both datasets share the same view.

```javascript
document.getElementById('nested').addEventListener('click', () => {
   if (data.datasets[1].hasOwnProperty("hidden") && data.datasets[1].hidden == true) {
      datasets[0][1].hidden = false;
      datasets[1][1].hidden = false;
   } else {
      datasets[0][1].hidden = true;
      datasets[1][1].hidden = true;
   }
   myChart.update();
});
```

<h3>Result</h3>

<canvas id="chart6" width="400" height="400"></canvas>

<button id="nested6" class="button">Toggle Nested</button>

<button id="dataset6" class="button">Switch Dataset</button>

<script>
   const datasets6 = [
      [{
         data: [300, 50, 100],
         backgroundColor: [
            'rgb(255, 99, 132)',
            'rgb(54, 162, 235)',
            'rgb(255, 205, 86)'
         ]
      }, {
         data: [150, 150, 50, 75, 25],
         backgroundColor: [
            'rgb(255, 99, 132)',
            'rgb(255, 99, 132)',
            'rgb(54, 162, 235)',
            'rgb(255, 205, 86)',
            'rgb(255, 205, 86)'
         ]
      }],
      [{
         data: [150, 100, 200],
         backgroundColor: [
            'rgb(255, 99, 132)',
            'rgb(54, 162, 235)',
            'rgb(255, 205, 86)'
         ]
      }, {
         data: [150, 25, 75, 100, 50, 50],
         backgroundColor: [
            'rgb(255, 99, 132)',
            'rgb(54, 162, 235)',
            'rgb(54, 162, 235)',
            'rgb(255, 205, 86)',
            'rgb(255, 205, 86)',
            'rgb(255, 205, 86)'
         ]
      }]
   ];

   const data6 = {
      labels: [
         'Red',
         'Blue',
         'Yellow'
      ],
      datasets: datasets6[0]
   };

   const ctx6 = document.getElementById('chart6').getContext('2d');

   const myChart6 = new Chart(ctx6, {
      type: 'doughnut',
      data: data6,
      options: {
         plugins: {
            tooltip: {
               enabled: true,
               callbacks: {
                  label: (ttItem) => {
                     let sum = 0;

                     let dataArr = ttItem.dataset.data;
                     dataArr.map(data => {
                     sum += Number(data);
                     });

                     let percentage = (ttItem.parsed * 100 / sum).toFixed(2) + '%';
                     return `${ttItem.parsed}: ${percentage}`;
                  }
               }
            },
            legend: {
               labels: {
                  generateLabels: chart => chart.data.labels.map((l, i) => ({
                     text: l,
                     index: i,
                     fillStyle: chart.data.datasets[0].backgroundColor[i],
                     strokeStyle: chart.data.datasets[0].backgroundColor[i],
                     hidden: chart.getDatasetMeta(0).data[i].hidden
                  })),
               },
               onClick: (event, legendItem, legend) => {
                  var start = 0;
                  var end = 0;
                  var sum = 0;
                  let chart = legend.chart;
                  let hidden = !chart.getDatasetMeta(0).data[legendItem.index].hidden;
                  chart.getDatasetMeta(0).data[legendItem.index].hidden = hidden;
                  chart.data.datasets[0].data.forEach((v, i) => {
                     var value = chart.getDatasetMeta(0).data[i].$context.parsed;
                     if (i == legendItem.index) {
                        start = sum;
                        end = sum + value;
                     }
                     sum += value;
                  });
                  sum = 0;
                  chart.data.datasets[1].data.forEach((v, i) => {
                     var value = chart.getDatasetMeta(1).data[i].$context.parsed;
                     sum += value;
                     if (sum > start && sum <= end) {
                     chart.getDatasetMeta(1).data[i].hidden = hidden;
                     }
                  });
                  chart.update();
               }
            }
         }
      }
   });

   document.getElementById('nested6').addEventListener('click', () => {
      if (data6.datasets[1].hasOwnProperty("hidden") && data6.datasets[1].hidden == true) {
         datasets6[0][1].hidden = false;
         datasets6[1][1].hidden = false;
      } else {
         datasets6[0][1].hidden = true;
         datasets6[1][1].hidden = true;
      }
      myChart6.update();
   });

   document.getElementById('dataset6').addEventListener('click', () => {
      if (datasets6.indexOf(data6.datasets) == -1 || datasets6.indexOf(data6.datasets) == 1) {
         data6.datasets = datasets6[0]
      } else {
         data6.datasets = datasets6[1]
      }
      myChart6.update();
   });
</script>

<h2>SondeHub Listener Stats API</h2>

The SondeHub Listener Stats API returns information about the number of receiver stations that have uploaded telemetry to the SondeHub radiosonde tracking database.

The API can be integrated with the chart to ensure that the latest information is always shown.

The specific Python code and Elasticsearch Query for generating the API response can be found <a href="https://github.com/projecthorus/sondehub-infra/blob/a70f7aac4c3b4745a1894d9c7a261830ea982fa3/lambda/query/__init__.py#L394">here</a>.

```
https://api.v2.sondehub.org/listeners/stats
```

```json
{
   "radiosonde_auto_rx": {
      "telemetry_count": 46867907,
      "unique_callsigns": 620,
      "versions": {
         "1.5.10": {
            "telemetry_count": 31942436,
            "unique_callsigns": 390
         },
         "1.5.9": {
            "telemetry_count": 5278005,
            "unique_callsigns": 73
         },
         "1.5.8": {
            "telemetry_count": 2554674,
            "unique_callsigns": 55
         }
      }
   },
   "rdzTTGOsonde": {
      "telemetry_count": 11768771,
      "unique_callsigns": 221,
      "versions": {
         "devel20220426": {
            "telemetry_count": 3854971,
            "unique_callsigns": 71
         },
         "master_v0.9.1": {
            "telemetry_count": 3628923,
            "unique_callsigns": 67
         },
         "devel20220123": {
            "telemetry_count": 1931191,
            "unique_callsigns": 33
         }
      }
   },
   "SondeMonitor": {
      "telemetry_count": 100335,
      "unique_callsigns": 10,
      "versions": {
         "6.2.6.1": {
            "telemetry_count": 26773,
            "unique_callsigns": 6
         },
         "6.2.5.8":{
            "telemetry_count": 184,
            "unique_callsigns": 2
         },
         "6.2.4.7":{
            "telemetry_count": 73262,
            "unique_callsigns": 1
         }
      }
   },
   "totals": {
      "unique_callsigns": 847,
      "telemetry_count": 58737217
   }
}
```

<h3>Swagger UI API</h3>

The following Swagger UI component can be used to access the SondeHub Listener Stats API and get real results.

<div id="OpenAPI"></div>

<style>
   .swagger-ui .wrapper {
      padding: 0px!important;
   }
   .swagger-ui .wrapper .col-12 {
      padding: 0px!important;
   }
   .swagger-ui .opblock .opblock-summary-path {
      max-width: 100%!important;
   }
   @media (max-width: 768px) {
      .swagger-ui .opblock-body select {
         min-width: 40%!important;
      }
   }
   .swagger-ui .info .title, .swagger-ui a.nostyle, .swagger-ui .parameter__name, .swagger-ui .parameter__type, .swagger-ui .parameter__deprecated, .swagger-ui .parameter__in, .swagger-ui table thead tr th, .swagger-ui .response-col_status, .swagger-ui table thead tr td, .swagger-ui .opblock .opblock-section-header h4, .swagger-ui label, .swagger-ui .tab li, .swagger-ui .opblock .opblock-section-header label, .swagger-ui .btn {
      color: #CCCCCC!important;
   }
   .swagger-ui .info .title, .swagger-ui .scheme-container, .swagger-ui select {
      background-color: #222!important;
      color: #CCC!important;
   }
   .swagger-ui .opblock .opblock-section-header {
      background-color: transparent!important;
   }
   .swagger-ui textarea, .swagger-ui input[type=text] {
      background-color: #41444e!important;
      color: #CCC!important;
   }
   .swagger-ui .topbar {
      background-color: #000!important;
   }
   .swagger-ui .info .base-url {
      color: #CCC!important;
   }
   .swagger-ui .scheme-container .schemes > label {
      color: #CCC!important;
   }
   .swagger-ui section.models h4 {
      color: #CCC!important;
   }
   .swagger-ui .model-title {
      color: #CCC!important;
   }
   .swagger-ui .model {
      color: #CCC!important;
   }
   .swagger-ui .opblock-tag {
      color: #CCC!important;
   }
   .swagger-ui a.nostyle, .swagger-ui a.nostyle:visited {
      color: #CCC!important;
   }
   .swagger-ui .opblock-description-wrapper p, .swagger-ui .opblock-external-docs-wrapper p, .swagger-ui .opblock-title_normal p {
      color: #CCC!important;
   }
   .swagger-ui .responses-inner h4, .swagger-ui .responses-inner h5 {
      color: #CCC!important;
   }
   .arrow {
      fill: #CCC!important;
   }
   #large-arrow-down {
      fill: #CCC!important;
   }
   .model-toggle collapsed {
      fill: #CCC!important;
   }
   .swagger-ui {
      color: #CCC!important;
   }
   .swagger-ui .prop-type {
      color: #aeaef2!important;
   }
   .swagger-ui .opblock-tag {
      border-bottom: 1px solid rgba(255, 255, 255, 0.3)!important;
   }
   .swagger-ui .model-toggle::after {
      display: block!important;
      width: 20px!important;
      height: 20px!important;
      content: ""!important;
      background: url("data:image/svg+xml;charset=utf-8,%3Csvg xmlns='http://www.w3.org/2000/svg' width='24' height='24' fill='%23FFFFFF' viewBox='0 0 24 24'%3E%3Cpath d='M10 6L8.59 7.41 13.17 12l-4.58 4.59L10 18l6-6z'/%3E%3C/svg%3E") 50% no-repeat!important;
      background-size: auto!important;
      background-size: 100%!important;
   }
   .swagger-ui select {
      font-size: 14px!important;
      font-weight: 700!important;
      padding: 5px 40px 5px 10px!important;
      border: 2px solid #41444e!important;
      border-radius: 4px!important;
      background-color: rgb(247, 247, 247)!important;
      background-size: auto!important;
      background-size: 20px!important;
      -webkit-box-shadow: 0 1px 2px 0 rgba(0, 0, 0, .25)!important;
      box-shadow: 0 1px 2px 0 rgba(0, 0, 0, .25)!important;
      font-family: sans-serif!important;
      color: #3b4151!important;
      -webkit-appearance: none!important;
      -moz-appearance: none!important;
      appearance: none!important;
   }
</style>

<script>
   const paths = {
      "/listeners/stats": {
         "get": {
            "tags": [
               "SondeHub Listener Stats"
            ],
            "summary": "Basic version stats",
            "description": "Use this to get stats on how many users are using specific software",
            "responses": {
               "200": {
                  "description": "Returns a dictionary of softwares and versions"
               },
            },
         }
      }
   };

   const spec = {
      'swagger': '2.0',
      'paths': paths,
      'host': 'api.v2.sondehub.org'
   };

   SwaggerUIBundle({
      spec: spec,
      domNode: document.querySelector('#OpenAPI')
   })
</script>

<h2>Working Example</h2>

The following JSFiddle contains a fully working demo incorportating everything discussed in this post.

You can also find the complete source code on the <a href="https://github.com/projecthorus/sondehub-listener-stats">sondehub-listener-stats</a> GitHub page.

<iframe width="100%" height="800px" src="//jsfiddle.net/cfwrzvta/embedded/result/" allowfullscreen="allowfullscreen" allowpaymentrequest frameborder="0"></iframe>