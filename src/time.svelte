<script>
    import { onMount } from 'svelte';
    import { Chart, ColumnSeries, SplineSeries, Category, DateTime, Tooltip,Legend } from '@syncfusion/ej2-charts';
    import { Browser } from '@syncfusion/ej2-base';
    Chart.Inject(ColumnSeries, SplineSeries, Category, DateTime, Tooltip,Legend);

    let chartData2 = [];
    let splineData = [];

    const monthNames = [
        '2023 January', 'February', 'March', 'April', 'May', 'June', 'July',
        'August', 'September', 'October'
    ];

    onMount(async () => {
        try {
            const response = await fetch('https://api.recruitly.io/api/dashboard/ats/data/placementmonthlymetrics?start=01%2F01%2F2023&end=11%2F10%2F2023&apiKey=TEST45684CB2A93F41FC40869DC739BD4D126D77');
            if (response.ok) {
                const jsonData = await response.json();

                const dataMap = new Map(jsonData.map(item => [monthNames[item.month - 1], item]));

                chartData2 = monthNames.map(month => ({
                    x: month,
                    y: dataMap.has(month) ? dataMap.get(month).placements : 0
                }));

                splineData = monthNames.map(month => ({
                    x: month,
                    y: dataMap.has(month) ? dataMap.get(month).days : 0
                }));

                updateChart();
            } else {
                console.error('Failed to fetch data from the API');
            }
        } catch (error) {
            console.error('An error occurred:', error);
        }
    });

    function updateChart() {
        const chart = new Chart({
            primaryXAxis: {
                valueType: 'Category',
                majorGridLines: { width: 0 },
                labelStyle: {
                    size: '17px',
                 fontWeight:"normal"
         
           }
        },
            primaryYAxis: {
                edgeLabelPlacement: 'Shift',
                majorTickLines: { width: 0 },
                minorTickLines: { width: 0 },
                lineStyle: { width: 0 },
                title: "Placements",
                titleStyle: {
                    fontFamily: "'Segoe UI', 'Helvetica Neue', 'Trebuchet MS', Verdana, sans-serif",
                  fontWeight: '360',
                  color: "#767676;",
                  size: '18px',
                 
                 },
                labelStyle: {
                    size: '15px',
                 fontWeight:"normal"
         
        
                
              
            }
        },
            width: '1390px',
            height: '400px',
            series: [
                {
                    type: 'Column',
                    dataSource: chartData2,
                    xName: 'x',
                    yName: 'y',
                    fill: 'DodgerBlue',
                    name: 'Placements',
                    columnWidth: 0.4,
                },
                {
                    type: 'Spline',
                    dataSource: splineData,
                    xName: 'x',
                    yName: 'y',
                    yAxisName: 'splineAxis',
                    width: 2,
                    fill: 'blue',
                    marker: {
                        visible: true,
                        width: 10,
                        height: 10
                    },
                    name: 'Avg.Days to fill',
                  
                },
            ],
            axes: [
                {
                    name: 'splineAxis',
                    opposedPosition: true,
                    title: 'Total time to fill days',
                    titleStyle: {
                    fontFamily: "'Segoe UI', 'Helvetica Neue', 'Trebuchet MS', Verdana, sans-serif",
                  fontWeight: '360',
                  color: "#767676;",
                  size: '18px',
                 
                 },
                    labelStyle: {
                    size: '15px',
                 fontWeight:"normal"
         
                    }
                  
                },
            ],
           
            tooltip: {
                    enable: true,
                    shared: true,
                    format: ' ${series.name} :${point.y}',
                    fill: 'white', // Change the background color of the tooltip
                    textStyle: {
                        color: 'black', // Change the text (content) color of the tooltip
                        fontWeight:"bold",
                    
                    
                    },
                    border: {
                        width: 4, // Set the border width
                        color: 'whitesmoke' // Set the border color
                    }
                 },
            legendSettings: {
        visible: true,
        //Legend position as top
        position:'Bottom'
        }
        });

        chart.appendTo('#container2');
    }
</script>

<div class="Container1">
    <h1 class="h1">Time to fill</h1>
<div id="container2"></div>
</div>
<style>
    .Container1{
        background-color:white;
        width: 1400px;
	    height: 430px;
        margin-left:40px;
        margin-top: 80px;
       
    }
    .h1{
    font-size: 17px;
    font-weight: bold;
    margin-left: -1220px;
    margin-bottom: 20px;

 }

</style>