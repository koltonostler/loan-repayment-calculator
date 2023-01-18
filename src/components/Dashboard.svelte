<script>
    export let avalancheData;
    export let snowballData;
    export let minPaymentData;
    export let showMin;
</script>

<div class="grid">

    <article>
        <h4>Avalanche Approach <span class="info" data-tooltip="Pay off your loans from highest interest rate to lowest interest rate">&#9432</span></h4>
    
        <label for="avalanche-time">Total time to pay off all loans</label>
        <span id="avalanche-time">{(avalancheData[0] / 12).toFixed(2)} years</span>
        <label for="avalanche-interest">Total interest paid</label>
        <span id="avalanche-interest">${(avalancheData[1]).toFixed(2)}</span>
        <label for="avalanche-total">Total amount paid</label>
        <span id="avalanche-total">${(avalancheData[3]).toFixed(2)}</span>
    
    </article>
    <article>
        <h4>Snowball Approach <span class="info" data-tooltip="Pay off your loans from smallest balance to highest balance">&#9432</span></h4>
    
        <label for="snowball-time">Total time to pay off all loans</label>
        <span id="snowball-time">{(snowballData[0] / 12).toFixed(2)} years</span>
        <label for="snowball-interest">Total interest paid</label>
        <span id="snowball-interest">${(snowballData[1]).toFixed(2)}</span>
        <label for="snowball-total">Total amount paid</label>
        <span id="snowball-total">${(snowballData[3]).toFixed(2)}</span>
    
    </article>
    {#if showMin}
        <article>
            <h4>Minimum Payments <span class="info" data-tooltip="Only making minimum payments on all loans">&#9432</span></h4>
        
            <label for="min-time">Total time to pay off all loans</label>
            <span id="min-time">{(minPaymentData[0] / 12).toFixed(2)} years</span>
            <label for="min-interest">Total interest paid</label>
            <span id="min-interest">${(minPaymentData[1]).toFixed(2)}</span>
            <label for="min-total">Total amount paid</label>
            <span id="min-total">${(minPaymentData[2]).toFixed(2)}</span>
        
        </article>
    {/if}
</div>

<style>
    .info {
        font-size: .95rem;
    }
    article {
        margin: 1rem 0rem;
        padding: 2rem;
        text-align: center;
    }
    label {
        margin-top: 1rem;
    }
    span {
        font-weight: bold;
    }

</style>