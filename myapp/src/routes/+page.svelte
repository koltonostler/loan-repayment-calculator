<script>
	import { each } from "svelte/internal";
import Calculations from "../components/Calculations.svelte";
import Chart from "../components/Chart.svelte";
import Dashboard from "../components/Dashboard.svelte";
import LoanInput from "../components/LoanInput.svelte";

let loans = [
    {id: 1, canDelete: false, loanAmount: 30000, rate: 6, minPayment: 200},  
]

let loansArr = [];
let additionalPayment = 0;

let monthlyPayment = loans.reduce((acc, curr) => {
	return acc + curr.minPayment;
}, additionalPayment);


class Loan {
	constructor(loanAmount, interestRate, minPayment) {
		this.loanAmount = loanAmount;
		this.interestRate = interestRate / 100;
		this.minPayment = minPayment;
	}

	clone() {
		return new Loan(this.loanAmount, this.interestRate * 100, this.minPayment);
	}

	// additional payment is in addition to the min payment
	makePayment(additionalPayment = 0) {
		// calculate interest first
		let interestPaid = this.loanAmount * (this.interestRate / 12);

		this.loanAmount += interestPaid;
		// subtract minPayment + additialPayment

		this.loanAmount -= this.minPayment + additionalPayment;

		return interestPaid;
	}
}

const handleRemove = (id) => {
    loans = loans.filter((loan) => {
        if (loan.id !== id) {
            return loan;
        }
        
    });
    for (let i = 0; i < loans.length; i++) {
        loans[i].id = i + 1;
    }
}

const handleAddLoan = () => {
    let index = loans.length;
    loans[index] = {id: index + 1, canDelete: true}
}



const handleCalcLoans = () => {
    for (let loan of loans) {
        loansArr.push(new Loan(loan.loanAmount, loan.rate, loan.minPayment));
    }
    console.log(loans);
}


</script>

<body class="container"> 
    <main> 
        {#each loans as {id, loanAmount, rate, minPayment}}
            <div>
                id: {id}, loanAmount: {loanAmount}, rate: {rate}, minPayment: {minPayment}
            </div>
        {/each}
        {additionalPayment}
        <h1>Student Loan Repayment Calculator</h1>  
        <section>
            <h3>Enter Loan Information</h3>
            <form id="myForm" on:submit|preventDefault = {handleCalcLoans}>
                {#each loans as {id, canDelete, loanAmount, rate, minPayment}}
                    <LoanInput loanId={id} canDelete={canDelete} bind:loanAmount={loanAmount} bind:rate={rate} bind:minPayment={minPayment} removeFn={()=> {
                        handleRemove(id)
                    }}/>
                {/each}
                
            </form>
            <div class="grid">
                <div></div>
                <button on:click|preventDefault = {handleAddLoan}>Add Another Loan</button>             
                <div></div>
            </div>
            <div class="grid">
                <div>
                    <label for="add-payment">Additional Payment ($)</label>
                    <input bind:value={additionalPayment} type="number" id="add-payment" min="0">
                </div>
                <div>
                    <label for="month-payment">Total Monthly Payment</label>
                    <div>${loans.reduce((acc, curr) => {
                        return acc + curr.minPayment;
                    }, additionalPayment)}</div>
                </div>
                <div>
                    <label for="loan-amount">Total Loan Amount</label>
                    <div>${loans.reduce((acc, curr) => {
                        return acc + curr.loanAmount;
                    }, 0)}</div>
                </div>
            </div>
            <div class="grid">
                <div></div>
                <button type="submit" form="myForm">Calculate</button>
                <div></div>
            </div>
        </section> 
        
        <section>
            <Dashboard />
            <Chart/>
        </section>
    </main>
    <footer>

    </footer>
</body>


<style>
    h3 {
        text-align: center;
    }
</style>






