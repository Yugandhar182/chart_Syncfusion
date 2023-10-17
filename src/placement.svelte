<script>
    import { onMount } from 'svelte';
    import { Chart, StackingColumnSeries, Category, DataLabel, Tooltip, Legend,Highlight } from '@syncfusion/ej2-charts';
    import { Browser } from '@syncfusion/ej2-base';

    Chart.Inject(StackingColumnSeries, Category, DataLabel, Tooltip, Legend,Highlight);

    let chartData = []; // Initialize with an empty array
    import { dateStore } from './DateStore.js'; // Import the store

  let startDate = '';
  let endDate = '';

  // Function to check if local storage has date information
  function getDatesFromLocalStorage() {
    const storedStartDate = localStorage.getItem('startDate');
    const storedEndDate = localStorage.getItem('endDate');

    if (storedStartDate && storedEndDate) {
      startDate = storedStartDate;
      endDate = storedEndDate;
    }
  }

  // Subscribe to the dateStore
  dateStore.subscribe((value) => {
    startDate = value.startDate;
    endDate = value.endDate;
    fetchData(startDate, endDate); // Fetch data whenever the date changes
  });

   
 
    async function fetchData(startDate, endDate) {
   
        try {
            const apiUrl = "https://api.recruitly.io/api/dashboard/ats/data/placementmonthlyusermetrics";
            const apiKey = "TEST45684CB2A93F41FC40869DC739BD4D126D77";
            const response = await fetch(`${apiUrl}?start=${startDate}&end=${endDate}&apiKey=${apiKey}`);

            if (response.ok) {
                const jsonData = await response.json();
                console.log(jsonData);

                // Transform API data into the format suitable for the chart
                chartData = jsonData
                    .filter(item => item.monthLabel) // Filter out items with undefined monthLabel
                    .map(item => ({
                        x: getMonthName(parseInt(item.monthLabel.split('/')[0]) - 1), // Convert numeric month to month name
                        'Andy Barnes': item['Andy Barnes'] / 1000000, // Convert to millions
                        'Gary Williams': item['Gary Williams'] / 1000000, // Convert to millions
                        'Bob Shaw': item['Bob Shaw'] / 1000000, // Convert to millions
                    }));

                // Sort the chart data by month order
                chartData.sort((a, b) => {
                    return getMonthIndex(a.x) - getMonthIndex(b.x);
                });

                console.log(chartData);
                // Refresh the chart with the updated data
                updateChart();
            } else {
                console.error('Failed to fetch data from the API. HTTP status:', response.status);
            }
        } catch (error) {
            console.error('An error occurred while fetching data:', error);
        }
    }
    function getMonthName(monthIndex) {
        const options = { month: 'long' };
        return new Date(2000, monthIndex, 1).toLocaleDateString(undefined, options);
    }

    // Function to get the month index from the month name
    function getMonthIndex(monthName) {
        const options = { month: 'long' };
        const date = new Date(2000, 0, 1);
        while (date.toLocaleDateString(undefined, options) !== monthName) {
            date.setMonth(date.getMonth() + 1);
        }
        return date.getMonth();
    }

    // Function to create or update the chart
    function updateChart() {
        // Create and append the chart with the updated data source
        const chart = new Chart({
            primaryXAxis: {
                valueType: 'Category',
                majorGridLines: { width: 0 },
                
                labelStyle: {
                    size: '15px',
                 fontWeight:"normal"
                },
                
            },
            primaryYAxis: {
                edgeLabelPlacement: 'Shift',
                majorTickLines: { width: 0 },
                minorTickLines: { width: 0 },
                lineStyle: { width: 0 },
                labelFormat: '{value}M',
                labelPlacement: 'OnTicks',
                minimum: 0, maximum: 5, interval:1,
                title: 'Placement value',
                titleStyle: {
                    fontFamily: "'Segoe UI', 'Helvetica Neue', 'Trebuchet MS', Verdana, sans-serif",
                  fontWeight: 'Normal',
                  color: "#767676;",
                  size: '18px',
                 
                 },
                labelStyle: {
                    size: '15px',
                 fontWeight:"normal"
         
        
        },
            },
            width: '1350px', // Set the width of the chart
            height: '400px', // Set the height of the chart
            series: [
                {
                    type: 'StackingColumn',
                    dataSource: chartData,
                    xName: 'x',
                    width: 2,
                    yName: 'Bob Shaw',
                    columnSpacing: 0.2,
                    name: 'Bob Shaw',
                    fill: 'DodgerBlue',
                    columnWidth: 0.5,
                },
                {
                    type: 'StackingColumn',
                    dataSource: chartData,
                    xName: 'x',
                    width: 2,
                    yName: 'Andy Barnes',
                    columnSpacing: 0.2,
                    name: 'Andy Barnes',
                    fill: 'Tomato',
                    columnWidth: 0.5,
                },
                {
                    type: 'StackingColumn',
                    dataSource: chartData,
                    xName: 'x',
                    width: 2,
                    yName: 'Gary Williams',
                    columnSpacing: 0.2,
                    name: 'Gary Williams',
                    fill: 'MediumSeaGreen',
                    columnWidth: 0.5,
                },
            ],
        
            tooltip: {
                enable: true,
                format: 'GDP ${point.y}', // Customize tooltip format
                template:'#Female-Material'
               
              
            },
            legendSettings: { enableHighlight :true },

        });

        chart.appendTo('#container3');
    }
</script>
<div class="container1">
    <h1 class="h1">Placement Value by User</h1>
<div id='container3'></div>
</div>
<style>
       .container1{
        background-color:white;
        width: 1400px;
	    height: 430px;
        margin-left:40px;
        margin-top: 80px;
       
    }
     .h1{
    font-size: 17px;
    font-weight: bold;
    margin-left: -1120px;
    margin-bottom: 20px;

 }
</style>
