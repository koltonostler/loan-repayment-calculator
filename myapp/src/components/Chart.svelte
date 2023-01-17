
<script>
    import Chart from 'chart.js/auto'
    export let labels;
    export let avalancheChartData;
    export let snowballChartData;
    export let minPaymentChartData;
    const data1 = {
        label: 'Avalanche Approach',
        data: avalancheChartData,
        fill: false,
        borderColor: 'rgb(75, 192, 192)',
        tension: 0.1
        
    };
    
    const data2 = {     
        label: 'Snowball Approach',
        data: snowballChartData,
        fill: false,
        borderColor: 'rgb(5, 12, 192)',
        tension: 0.1   
    };

    const data3 = {     
        label: 'Minimum Payments',
        data: minPaymentChartData,
        fill: false,
        borderColor: 'rgb(255, 12, 45)',
        tension: 0.1   
    };
    let myChart = null;
    const dataSets = [data1, data2, data3]

    async function handleCreateChart(myChart) {
        myChart = await generateChart(dataSets, labels)
    }

   
    async function generateChart(data, labels) {    
        let ctx = document.getElementById('myChart');
        
        let myChart = new Chart(
            ctx,
            {
                type: 'line',
                data: {
                    labels: labels,
                    datasets: data
                },
            }
        );

        return myChart;
    };
</script>



<button on:click|preventDefault = {()=> {handleCreateChart(myChart)}} id="chartBtn">Create Chart
</button>

<article class= "chart-container">
        <div style="width: 800px;"><canvas id="myChart"></canvas></div>
</article>

    
<style>
    .chart-container {
        display: flex; 
        justify-content: center;
        align-items: center;
    }
</style>





