<script>
    import { onMount, afterUpdate } from 'svelte';
    import { dateStore } from './DateStore.js'; // Import the store
    import { Chart, BarSeries, Category, DataLabel } from '@syncfusion/ej2-charts';
    Chart.Inject(BarSeries, Category, DataLabel);

 
    let chartData = [];
    let chart = null; // Initialize chart with null
  
  
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
        const apiUrl = "https://api.recruitly.io/api/dashboard/ats/data/rejectreasons";
        
        const apiKey = "TEST45684CB2A93F41FC40869DC739BD4D126D77";
        const response = await fetch(`${apiUrl}?start=${startDate}&end=${endDate}&apiKey=${apiKey}`);
  
        if (response.ok) {
          const jsonData = await response.json();
          console.log("Data fetched:", jsonData);
          chartData = jsonData.slice(0, 6).map((item, index) => ({
            x: item.reason || 'Unknown',
            y: item.count,
          
          }));
          chartData.sort((a, b) => a.y - b.y);
          updateChart();
        } else {
          const errorText = await response.text();
          console.error("Failed to fetch data. Error:", errorText);
        }
      } catch (error) {
        console.error("Error:", error);
      }
    }
  
   
    function updateChart() {
      const data = chartData.map(item => ({ ...item }));
      if (chart) {
        chart.destroy();
      }
      chart = new Chart({
        primaryXAxis: {
          valueType: 'Category',
          majorGridLines: { width: 0 },
          labelStyle: {
            size: '1rem',
            fontWeight: "510",
            fontFamily: 'sans-serif',
          },
        },
        primaryYAxis: {
          majorTickLines: { width: 0 },
          minorTickLines: { width: 0 },
          lineStyle: { width: 0 },
          interval: 5,
          labelStyle: {
            size: '1rem',
            fontWeight: "400",
            fontFamily: 'sans-serif'
          },
        },
        width: '1350px',
        height: "390px",
        series: [
          {
            type: 'Bar',
            dataSource: chartData,
            xName: 'x',
            width: 2,
            yName: 'y',
            columnSpacing: 0.1,
            animation: {
              enable: true,
              duration: 1000,
              delay: 200
            },
            cornerRadius: {
              topRight: 5,
              bottomRight: 5
            },
            marker: {
              dataLabel: {
                visible: true,
                position: "Bottom",
                font: {
                  fontWeight: 'bold',
                  color: 'white',
                  size: '17px'
                }
              }
            },
          },
        ],
      });
  
      chart.appendTo('#container');
    }
  
    onMount(() => {
      getDatesFromLocalStorage(); // Try to get dates from local storage
      fetchData(startDate, endDate); // Fetch data on component mount
    });
  
    afterUpdate(() => {
      fetchData(startDate, endDate); // Fetch data after updates
      updateChart(); // Update the chart immediately
  
      // Store the dates in local storage for future use
      localStorage.setItem('startDate', startDate);
      localStorage.setItem('endDate', endDate);
    });
  </script>
  
  <div id='container'></div>
  <style>
    #container {
        margin-top:100px; 
        margin-bottom: 20px; 
    }
  </style>
