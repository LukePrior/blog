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

## Chart.js

Chart.js is a simple JavaScript charting library for creating responsive visualisations of various types and styles.

Chart.js can easily be added on an existing website by including the latest version of the library from a CDN or locally.

```html
<script type="text/javascript" language="javascript" src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/3.7.1/chart.js"></script>
```

### Donut Chart

To render a chart a canvas element needs to be added to the website

```html
<canvas id="myChart" width="400" height="400"></canvas>
```

The graph requires a data object which contains the dataset and formatting options for the graph.

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

#### Result

The complete code for this example can be found <a href="https://jsfiddle.net/vzg1eh8o/" target="_blank">here</a>.

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

### Custom Item Tooltips

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

#### Result

The complete code for this example can be found <a href="https://jsfiddle.net/awfkm86x/" target="_blank">here</a>.

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

### Nested Donut Chart

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

#### Result

The complete code for this example can be found <a href="https://jsfiddle.net/wLe7ngk3/" target="_blank">here</a>.

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

#### Result

The complete code for this example can be found <a href="https://jsfiddle.net/qkxyjfuL/" target="_blank">here</a>.

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

### Toggle Nested Data

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

#### Result

The complete code for this example can be found <a href="https://jsfiddle.net/23k567q1/" target="_blank">here</a>.

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

### Switch datasets

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

#### Result

The complete code for this example can be found <a href="https://jsfiddle.net/1crxpez4/" target="_blank">here</a>.

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

### Inner text

To display additional text within the donut chart a Chart.js plugin must be created.

This plugin can be assigned to any graph and can be customised to display specific text with automatic resising to remain within the available space.

The specific plugin used is a slight modification to the <a href="https://stackoverflow.com/a/43026361/9389353" target="_blank">answer</a> posted by Shawn Corrigan.

The plugin script will calculate the available width and adjust the font-size and line-breaks so that the text fills the available space.

```javascript
const countPlugin = {
  id: 'doughnut-centertext',
  beforeDraw: function(chart) {
    if (chart.config.options.elements.center) {
      // Get ctx from string
      var ctx = chart.ctx;

      var innerRadius = chart._metasets[chart._metasets.length - 2].controller.innerRadius;
      if (chart._metasets[chart._metasets.length - 1].controller.innerRadius > 0) {
        innerRadius = chart._metasets[chart._metasets.length - 1].controller.innerRadius;
      }

      // Get options from the center object in options
      var centerConfig = chart.config.options.elements.center;
      var fontStyle = centerConfig.fontStyle || 'Arial';
      var txt = centerConfig.text;
      var color = centerConfig.color || '#000';
      var maxFontSize = centerConfig.maxFontSize || 75;
      var sidePadding = centerConfig.sidePadding || 20;
      var sidePaddingCalculated = (sidePadding / 100) * (innerRadius * 2)
      // Start with a base font of 30px
      ctx.font = "30px " + fontStyle;

      // Get the width of the string and also the width of the element minus 10 to give it 5px side padding
      var stringWidth = ctx.measureText(txt).width;
      var elementWidth = (innerRadius * 2) - sidePaddingCalculated;


      // Find out how much the font can grow in width.
      var widthRatio = elementWidth / stringWidth;
      var newFontSize = Math.floor(30 * widthRatio);
      var elementHeight = (innerRadius * 2);

      // Pick a new font size so it will not be larger than the height of label.
      var fontSizeToUse = Math.min(newFontSize, elementHeight, maxFontSize);
      var minFontSize = centerConfig.minFontSize;
      var lineHeight = centerConfig.lineHeight || 25;
      var wrapText = false;

      if (minFontSize === undefined) {
        minFontSize = 30;
      }

      if (minFontSize && fontSizeToUse < minFontSize) {
        fontSizeToUse = minFontSize;
        wrapText = true;
      }

      // Set font settings to draw it correctly.
      ctx.textAlign = 'center';
      ctx.textBaseline = 'middle';
      var centerX = ((chart.chartArea.left + chart.chartArea.right) / 2);
      var centerY = ((chart.chartArea.top + chart.chartArea.bottom) / 2);
      ctx.font = fontSizeToUse + "px " + fontStyle;
      ctx.fillStyle = color;

      if (!wrapText) {
        ctx.fillText(txt, centerX, centerY);
        return;
      }

      var words = txt.split(' ');
      var line = '';
      var lines = [];

      // Break words up into multiple lines if necessary
      for (var n = 0; n < words.length; n++) {
        var testLine = line + words[n] + ' ';
        var metrics = ctx.measureText(testLine);
        var testWidth = metrics.width;
        if (testWidth > elementWidth && n > 0) {
          lines.push(line);
          line = words[n] + ' ';
        } else {
          line = testLine;
        }
      }

      // Move the center up depending on line height and number of lines
      centerY -= (lines.length / 2) * lineHeight;

      for (var n = 0; n < lines.length; n++) {
        ctx.fillText(lines[n], centerX, centerY);
        centerY += lineHeight;
      }
      //Draw text in center
      ctx.fillText(line, centerX, centerY);
    }
  }
};
```

The chart options also need to be updated to include the displayed text and styling options.

```javascript
plugins: [countPlugin],
options: {
   elements: {
      center: {
         text: "Inner Text",
         color: '#00a3d3',
         fontStyle: 'Arial',
         sidePadding: 30,
         minFontSize: false
      }
   }
}
```

#### Result

The complete code for this example can be found <a href="https://jsfiddle.net/xdam3tj9/" target="_blank">here</a>.

<canvas id="chart7" width="400" height="400"></canvas>

<button id="nested7" class="button">Toggle Nested</button>

<button id="dataset7" class="button">Switch Dataset</button>

<script>
   const countPlugin2 = {
      id: 'doughnut-centertext',
      beforeDraw: function(chart) {
         if (chart.config.options.elements.center) {
            var ctx = chart.ctx;

            var innerRadius = chart._metasets[chart._metasets.length - 2].controller.innerRadius;
            if (chart._metasets[chart._metasets.length - 1].controller.innerRadius > 0) {
               innerRadius = chart._metasets[chart._metasets.length - 1].controller.innerRadius;
            }

            var centerConfig = chart.config.options.elements.center;
            var fontStyle = centerConfig.fontStyle || 'Arial';
            var txt = centerConfig.text;
            var color = centerConfig.color || '#000';
            var maxFontSize = centerConfig.maxFontSize || 75;
            var sidePadding = centerConfig.sidePadding || 20;
            var sidePaddingCalculated = (sidePadding / 100) * (innerRadius * 2);
            ctx.font = "30px " + fontStyle;

            var stringWidth = ctx.measureText(txt).width;
            var elementWidth = (innerRadius * 2) - sidePaddingCalculated;

            var widthRatio = elementWidth / stringWidth;
            var newFontSize = Math.floor(30 * widthRatio);
            var elementHeight = (innerRadius * 2);

            var fontSizeToUse = Math.min(newFontSize, elementHeight, maxFontSize);
            var minFontSize = centerConfig.minFontSize;
            var lineHeight = centerConfig.lineHeight || 25;
            var wrapText = false;

            if (minFontSize === undefined) {
               minFontSize = 30;
            }

            if (minFontSize && fontSizeToUse < minFontSize) {
               fontSizeToUse = minFontSize;
               wrapText = true;
            }

            ctx.textAlign = 'center';
            ctx.textBaseline = 'middle';
            var centerX = ((chart.chartArea.left + chart.chartArea.right) / 2);
            var centerY = ((chart.chartArea.top + chart.chartArea.bottom) / 2);
            ctx.font = fontSizeToUse + "px " + fontStyle;
            ctx.fillStyle = color;

            if (!wrapText) {
               ctx.fillText(txt, centerX, centerY);
               return;
            }

            var words = txt.split(' ');
            var line = '';
            var lines = [];

            for (var n = 0; n < words.length; n++) {
               var testLine = line + words[n] + ' ';
               var metrics = ctx.measureText(testLine);
               var testWidth = metrics.width;
               if (testWidth > elementWidth && n > 0) {
                  lines.push(line);
                  line = words[n] + ' ';
               } else {
                  line = testLine;
               }
            }

            centerY -= (lines.length / 2) * lineHeight;

            for (var n = 0; n < lines.length; n++) {
               ctx.fillText(lines[n], centerX, centerY);
               centerY += lineHeight;
            }

            ctx.fillText(line, centerX, centerY);
         }
      }
   };

   const datasets7 = [
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
         data: [50, 100, 200],
         backgroundColor: [
            'rgb(255, 99, 132)',
            'rgb(54, 162, 235)',
            'rgb(255, 205, 86)'
         ]
      }, {
         data: [50, 25, 75, 100, 50, 50],
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

   const data7 = {
      labels: [
         'Red',
         'Blue',
         'Yellow'
      ],
      datasets: datasets7[0]
   };

   const ctx7 = document.getElementById('chart7').getContext('2d');

   const myChart7 = new Chart(ctx7, {
      type: 'doughnut',
      data: data7,
      plugins: [countPlugin2],
      options: {
         elements: {
            center: {
            text: "Inner Text",
            color: '#00a3d3',
            fontStyle: 'Arial',
            sidePadding: 30,
            minFontSize: false
            }
         },
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

   document.getElementById('nested7').addEventListener('click', () => {
      if (data7.datasets[1].hasOwnProperty("hidden") && data7.datasets[1].hidden == true) {
         datasets7[0][1].hidden = false;
         datasets7[1][1].hidden = false;
      } else {
         datasets7[0][1].hidden = true;
         datasets7[1][1].hidden = true;
      }
      myChart7.update();
   });

   document.getElementById('dataset7').addEventListener('click', () => {
      if (datasets7.indexOf(data7.datasets) == -1 || datasets7.indexOf(data7.datasets) == 1) {
         data7.datasets = datasets7[0]
      } else {
         data7.datasets = datasets7[1]
      }
      myChart7.update();
   });
</script>

## SondeHub Listener Stats API

The SondeHub Listener Stats API returns information about the number of receiver stations that have uploaded telemetry to the SondeHub radiosonde tracking database.

The API can be integrated with the chart to ensure that the latest information is always shown.

The specific Python code and Elasticsearch Query for generating the API response can be found <a href="https://github.com/projecthorus/sondehub-infra/blob/a70f7aac4c3b4745a1894d9c7a261830ea982fa3/lambda/query/__init__.py#L394" target="_blank">here</a>.

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

### Swagger UI API

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
   .swagger-ui a.nostyle, .swagger-ui a.nostyle:visited, .swagger-ui .responses-inner h4, .swagger-ui .responses-inner h5, .swagger-ui .opblock .opblock-section-header h4, .swagger-ui .opblock .opblock-section-header>label {
      color: var(--heading-color)!important;
   }
   .swagger-ui .opblock-description-wrapper p, .swagger-ui .opblock-external-docs-wrapper p, .swagger-ui .opblock-title_normal p, .swagger-ui table thead tr td, .swagger-ui table thead tr th, .swagger-ui .opblock .opblock-summary-description, .swagger-ui .response-col_status, .swagger-ui .markdown p, .swagger-ui .btn, .swagger-ui .parameter__name, .swagger-ui .parameter__type, .swagger-ui .parameter__extension, .swagger-ui .parameter__in {
      color: var(--text-color)!important;
   }
   .swagger-ui .opblock .opblock-section-header {
      background-color: var(--btn-box-shadow)!important;
   }
   .swagger-ui input {
      background-color: var(--btn-box-shadow)!important;
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

## Example

<blockquote class="prompt-danger"><p>The SondeHub Listener Stats API has been decomissioned rendering this example broken.</p></blockquote>

The following <a href="https://jsfiddle.net/634fonqk/" target="_blank">JSFiddle</a> contains a fully working demo incorportating everything discussed in this post.

You can also find the complete source code on the <a href="https://github.com/projecthorus/sondehub-listener-stats" target="_blank">sondehub-listener-stats</a> GitHub page.

<div id="container">
   <canvas id="chartJSContainer" style="display: none;" width="400" height="400"></canvas>
   <div id="loadingGif" style="display: block;" width="400" height="400">

      <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" style="margin: auto; background: rgb(255, 255, 255, 0); display: block; shape-rendering: auto;" width="100%" height="100%" viewBox="0 0 100 100" preserveAspectRatio="xMidYMid">
         <circle cx="50" cy="50" r="32" stroke-width="8" stroke="#00a3d3" stroke-dasharray="50.26548245743669 50.26548245743669" fill="none" stroke-linecap="round">
            <animateTransform attributeName="transform" type="rotate" repeatCount="indefinite" dur="1s" keyTimes="0;1" values="0 50 50;360 50 50"></animateTransform>
         </circle>
      </svg>

   </div>
   <div>
      <button id="update" class="button">MODE</button>
      <button id="hide" class="button">VERSIONS</button>
   </div>
</div>

<script>
   var count = [];
   var countNested = [];
   var countUnique = [];
   var countNestedUnique = [];
   var empty = [];
   var countSelected = true;
   var nestedSelected = false;
   var stationCount = '';

   var programs = [];
   var versions = [];
   var versionPrograms = [];
   var colours = ['#e41a1c', '#377eb8', '#4daf4a', '#984ea3', '#ff7f00', '#ffff33', '#a65628', '#f781bf', '#999999'];
   var excluded = ['Sonde Tracker', 'TTGO EMA DTCEA', 'TTGO WX'];

   var visibility = {};

   var getJSON = function(url, callback) {
      var xhr = new XMLHttpRequest();
      xhr.open('GET', url, true);
      xhr.responseType = 'json';
      xhr.onload = function() {
         var status = xhr.status;
         if (status === 200) {
            callback(null, xhr.response);
         } else {
            callback(status, xhr.response);
         }
      };
      xhr.send();
   };

   function resizeArray(array, size) {
      var dataNew = [];
      var sum = array.reduce((a, b) => a + b);

      array.forEach(function(item, index) {
         var rounded = Math.round(item / sum * size);
         dataNew.push(rounded);
      });

      var difference = dataNew.reduce((a, b) => a + b) - size;
      dataNew[0] = dataNew[0] - difference;

      return dataNew;
   };

   const countPlugin = {
      id: 'doughnut-centertext',
      beforeDraw: function(chart) {
         if (chart.config.options.elements.center) {
            var ctx = chart.ctx;

            var innerRadius = chart._metasets[chart._metasets.length - 2].controller.innerRadius;
            if (chart._metasets[chart._metasets.length - 1].controller.innerRadius > 0) {
               innerRadius = chart._metasets[chart._metasets.length - 1].controller.innerRadius;
            }

            var centerConfig = chart.config.options.elements.center;
            var fontStyle = centerConfig.fontStyle || 'Arial';
            var txt = centerConfig.text;
            var color = centerConfig.color || '#000';
            var maxFontSize = centerConfig.maxFontSize || 75;
            var sidePadding = centerConfig.sidePadding || 20;
            var sidePaddingCalculated = (sidePadding / 100) * (innerRadius * 2);
            ctx.font = "30px " + fontStyle;

            var stringWidth = ctx.measureText(txt).width;
            var elementWidth = (innerRadius * 2) - sidePaddingCalculated;

            var widthRatio = elementWidth / stringWidth;
            var newFontSize = Math.floor(30 * widthRatio);
            var elementHeight = (innerRadius * 2);

            var fontSizeToUse = Math.min(newFontSize, elementHeight, maxFontSize);
            var minFontSize = centerConfig.minFontSize;
            var lineHeight = centerConfig.lineHeight || 25;
            var wrapText = false;

            if (minFontSize === undefined) {
               minFontSize = 30;
            }

            if (minFontSize && fontSizeToUse < minFontSize) {
               fontSizeToUse = minFontSize;
               wrapText = true;
            }

            ctx.textAlign = 'center';
            ctx.textBaseline = 'middle';
            var centerX = ((chart.chartArea.left + chart.chartArea.right) / 2);
            var centerY = ((chart.chartArea.top + chart.chartArea.bottom) / 2);
            ctx.font = fontSizeToUse + "px " + fontStyle;
            ctx.fillStyle = color;

            if (!wrapText) {
               ctx.fillText(txt, centerX, centerY);
               return;
            }

            var words = txt.split(' ');
            var line = '';
            var lines = [];

            for (var n = 0; n < words.length; n++) {
               var testLine = line + words[n] + ' ';
               var metrics = ctx.measureText(testLine);
               var testWidth = metrics.width;
               if (testWidth > elementWidth && n > 0) {
                  lines.push(line);
                  line = words[n] + ' ';
               } else {
                  line = testLine;
               }
            }

            centerY -= (lines.length / 2) * lineHeight;

            for (var n = 0; n < lines.length; n++) {
               ctx.fillText(lines[n], centerX, centerY);
               centerY += lineHeight;
            }

            ctx.fillText(line, centerX, centerY);
         }
      }
   };

   getJSON('https://api.v2.sondehub.org/listener/stats', function(err, localData){
      stationCount = localData.totals.unique_callsigns + " Stations";
      for (const [key, value] of Object.entries(localData)) {
         if (key != "totals" && !excluded.includes(key)) {
            programs.push(key);
            count.push(value['unique_callsigns']);
            countUnique.push(value['telemetry_count']);
            var tempCount = [];
            var tempCountUnique = [];
            visibility[key] = false;
            for (const [key1, value1] of Object.entries(localData[key]['versions'])) {
               tempCount.push(value1['unique_callsigns']);
               tempCountUnique.push(value1['telemetry_count']);
               versions.push(key + " " + key1);
               versionPrograms.push(key);
            }
            countNested = countNested.concat(resizeArray(tempCount, value['unique_callsigns']));
            countNestedUnique = countNestedUnique.concat(resizeArray(tempCountUnique, value['telemetry_count']));
         }
      }
      data.datasets[1].data = countNested;
      options.options.elements.center.text = stationCount;
      loadChart();
   });  

   var data = {
      labels: programs,
      datasets: [{
            name: "Program",
            data: count,
            backgroundColor: colours,
            label: programs
         },
         {
            name: "Version",
            data: countNested,
            backgroundColor: colours,
            label: versions,
            program: versionPrograms,
            hidden: true
         }
      ]
   };

   var options = {
      type: 'doughnut',
      data: data,
      plugins: [countPlugin],
      options: {
         elements: {
            center: {
            text: stationCount,
            color: '#00a3d3',
            fontStyle: 'Arial',
            sidePadding: 30,
            minFontSize: false
            }
         },
         responsive: true,
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
               let version = legendItem.text;
               if (version == null) version = false;
               visibility[version] = !legendItem.hidden;
               let chart = legend.chart;
               let hidden = !chart.getDatasetMeta(0).data[legendItem.index].hidden;
               chart.getDatasetMeta(0).data[legendItem.index].hidden = hidden;
               let pointer = 0;
               chart.data.datasets[1].data.forEach((v, i) => {
                  var show = (visibility[versionPrograms[i]]);
                  chart.getDatasetMeta(1).data[i].hidden = show;
               });
               chart.update();
            }
            },
            tooltip: {
            enabled: true,
            callbacks: {
               footer: (ttItem) => {
                  let sum = 0;
                  let dataArr = ttItem[0].dataset.data;
                  dataArr.map(data => {
                     sum += Number(data);
                  });

                  let percentage = (ttItem[0].parsed * 100 / sum).toFixed(2) + '%';
                  if (countSelected) {
                     return `Percentage of stations: ${percentage}`;
                  } else {
                     return `Percentage of data: ${percentage}`;
                  }
               },
               title: (ttItem) => {
                  return ttItem[0].dataset.label[ttItem[0].dataIndex];
               },
               label: (ttItem) => {
                  var suffix = "";
                  if (countSelected) {
                     suffix = " stations";
                  } else {
                     suffix = " packets";
                  }
                  return ttItem.formattedValue + suffix;
               }
            }
            }
         }
      }
   };

   const ctx = document.getElementById('chartJSContainer').getContext('2d');
   const chart = new Chart(ctx, options);

   function loadChart() {
      document.getElementById('loadingGif').style.display = "none";
      document.getElementById('chartJSContainer').style.display = "block";
      chart.update();
   };

   document.getElementById('update').addEventListener('click', () => {
      if (countSelected) {
         data.datasets[0].data = countUnique;
         data.datasets[1].data = countNestedUnique;
         countSelected = false;
      } else {
         data.datasets[0].data = count;
         data.datasets[1].data = countNested;
         countSelected = true;
      }
      chart.update();
   });

   document.getElementById('hide').addEventListener('click', () => {
      if (nestedSelected) {
         data.datasets[1].hidden = true;
         nestedSelected = false;
      } else {
         data.datasets[1].hidden = false;
         nestedSelected = true;
      }
      chart.update();
   });
</script>