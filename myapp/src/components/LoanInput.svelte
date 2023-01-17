<script>
    export let loanId;
    export let canDelete;
    export let removeFn;
    export let loanAmount = 0;
    export let rate = 0;
    export let minPayment = 0;



</script>


<div class="loan-container">
    <h5>Loan {loanId}</h5>
    {#if canDelete}
        <button on:click|preventDefault = {removeFn} class="material-symbols-outlined outline contrast close-btn">
            close
        </button>
    {/if}
</div>

<div class="grid">
    <div>
        <label for="loan-amount-{loanId}">Loan Amount ($)</label>       
        <input type="number" min="0"  id="loan-amount-{loanId}" bind:value={loanAmount} required>
    </div>
    <div>
        <label for="rate-{loanId}">Interest Rate (%)</label>
        <input type="number" step="0.1" min="0" max ="99" id="rate-{loanId}" bind:value = {rate} required>
    </div>
    <div>
        <label for="min-payment-{loanId}">Minimum Payment ($)</label>
        <input type="number" min="0" id="min-payment-{loanId}" bind:value ={minPayment} required>
    </div>                    
</div>


<style>
    .close-btn {
        border: none;
        width: 50px;
        display: flex;
        justify-content: center;
        align-items: center;
        font-size: 1.5rem;
        height: 50px;
    }
    .loan-container {
        display:flex;
        justify-content: space-between;
    }
</style>