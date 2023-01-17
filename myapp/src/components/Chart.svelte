
<script>
    import Chart from 'chart.js/auto'
    const data1 = {
        label: 'My First Dataset',
        data: [65, 59, 80, 81, 56, 55, 40],
        fill: false,
        borderColor: 'rgb(75, 192, 192)',
        tension: 0.1
        
    };
    
    const data2 = {     
        label: 'My second Dataset',
        data: [60, 9, 120, 51, 36, 45, 47],
        fill: false,
        borderColor: 'rgb(5, 12, 192)',
        tension: 0.1   
    };
    let myChart = null;
    const labels = [1,2,3,4,5,6,7]
    const dataSets = [data1, data2]

   
    async function handleCreateChart(data) {
        
        let ctx = document.getElementById('myChart');
        
        let myChart = new Chart(
            ctx,
            {
            type: 'line',
            data: {
                labels: labels,
                datasets: data
            }
            }
        );
        return myChart;
    };
</script>



<!-- <button on:click|preventDefault = {()=> {
    myChart = handleCreateChart(dataSets);
}} id="chartBtn">Create Chart
</button> -->

<article class="chart-container">
    <div  style="width: 800px;"><canvas id="myChart"></canvas></div>
</article>

    
<style>
    .chart-container {
        display: flex; 
        justify-content: center;
        align-items: center;
    }
</style>





