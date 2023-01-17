<script>
import Dashboard from "../components/Dashboard.svelte";
import LoanInput from "../components/LoanInput.svelte";
import Chart from 'chart.js/auto'
import Footer from "../components/Footer.svelte";

export const prerender = true;

let loans = [
    {id: 1, canDelete: false, loanAmount: 0, rate: 0, minPayment: 0},  
]

let loansArr = [];
let avalancheArr = [];
let snowballArr = [];
let additionalPayment = 0;
let avalancheData = [0, 0, 0, 0];
let snowballData = [0, 0, 0, 0];
let minPaymentData = [0, 0, 0];
let avalancheChartData = [];
let snowballChartData = [];
let minPaymentChartData = [];
let minMonths = [];
let avalancheMonths = [];
let snowballMonths = [];
let showMin = false;

let chartState = 0;
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
    let dataSets = [data1, data2, data3]


    function handleCreateChart() {
        if (chartState === 0) {
            if (showMin) {
                myChart = generateNewChart(dataSets, minMonths);
            } else {
                if (avalancheMonths.length > snowballMonths.length) {
                    myChart = generateNewChart(dataSets, avalancheMonths);
                } else {
                    myChart = generateNewChart(dataSets, snowballMonths);
                }
            }
            chartState = 1;
        } else if (chartState === 1) {
            myChart.destroy();
            if (showMin) {
                myChart = generateNewChart(dataSets, minMonths);
            } else {
                if (avalancheMonths.length > snowballMonths.length) {
                    myChart = generateNewChart(dataSets, avalancheMonths);
                } else {
                    myChart = generateNewChart(dataSets, snowballMonths);
                }
            }
        }
    }

    function generateNewChart(data, labels) {    
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

const calculateRollOverApproach = (loanArr, additionalPayment, chartData, monthsArr) => {
	let monthsToPayLoan = 0;
	let carryOver = 0;
	let interestPaid = 0;

	for (let i = 0; i < loanArr.length; i++) {
		while (loanArr[i].loanAmount > 0 && monthsToPayLoan < 480) {
			interestPaid += loanArr[i].makePayment(additionalPayment);
			if (loanArr[i].loanAmount < 0) {
				carryOver = -loanArr[i].loanAmount;
				loanArr[i].loanAmount = 0;
				additionalPayment += loanArr[i].minPayment;
			}
			for (let j = i + 1; j < loanArr.length; j++) {
				if (loanArr[j].loanAmount === 0) continue;

				interestPaid += loanArr[j].makePayment(carryOver);
				if (loanArr[j].loanAmount < 0) {
					carryOver = -loanArr[j].loanAmount;
					loanArr[j].loanAmount = 0;
					additionalPayment += loanArr[j].minPayment;
				} else {
					carryOver = 0;
				}
			}
			monthsToPayLoan++;
            monthsArr.push(monthsToPayLoan);

            chartData.push(
                loanArr.reduce((acc, curr) => {
                    return acc + curr.loanAmount;
                }, 0)
            );
			
		}
	}

	return [monthsToPayLoan, interestPaid, chartData];
}

function calculateMinPaymentsOnly(loanArr) {
	let monthsToPayLoan = 0;
	let carryOver = 0;
	let interestPaid = 0;

	for (let i = 0; i < loanArr.length; i++) {
		while (loanArr[i].loanAmount > 0 && monthsToPayLoan < 480) {
			interestPaid += loanArr[i].makePayment();
			if (loanArr[i].loanAmount < 0) {
				carryOver = -loanArr[i].loanAmount;
				loanArr[i].loanAmount = 0;
			}
			for (let j = i + 1; j < loanArr.length; j++) {
				if (loanArr[j].loanAmount === 0) continue;

				interestPaid += loanArr[j].makePayment(carryOver);
				if (loanArr[j].loanAmount < 0) {
					carryOver = -loanArr[j].loanAmount;
					loanArr[j].loanAmount = 0;
				} else {
					carryOver = 0;
				}
			}
			monthsToPayLoan++;
            minMonths.push(monthsToPayLoan);
            minPaymentChartData.push(
				loanArr.reduce((acc, curr) => {
					return acc + curr.loanAmount;
				}, 0),
			);
		}
	}

	return [monthsToPayLoan, interestPaid];
}



const handleCalcLoans = () => {

    loansArr = [];
    avalancheArr = [];
    snowballArr = [];
    avalancheChartData = [];
    snowballChartData = [];
    minPaymentChartData = [];
    minMonths = [];
    avalancheMonths = [];
    snowballMonths = [];
    for (let loan of loans) {
        loansArr.push(new Loan(loan.loanAmount, loan.rate, loan.minPayment));
    }

    for (let loan of loansArr) {
        avalancheArr.push(loan.clone());
        snowballArr.push(loan.clone());
    }


    avalancheArr.sort((a, b) => {
	    return b.interestRate - a.interestRate;
    });

    snowballArr.sort((a, b) => {
        return a.loanAmount - b.loanAmount;
    });

    let totalLoanAmnt = loans.reduce((acc, curr) => {
                        return acc + curr.loanAmount;
                    }, 0);

    avalancheData = calculateRollOverApproach(avalancheArr, additionalPayment, avalancheChartData, avalancheMonths)
    avalancheData.push(avalancheData[1] + totalLoanAmnt);
    snowballData = calculateRollOverApproach(snowballArr, additionalPayment, snowballChartData, snowballMonths);
    snowballData.push(snowballData[1] + totalLoanAmnt);
    minPaymentData = calculateMinPaymentsOnly(loansArr);  
    minPaymentData.push(minPaymentData[1] + totalLoanAmnt);
    data1.data = avalancheChartData;
    data2.data = snowballChartData;
    data3.data = minPaymentChartData;
    if (showMin) {
        dataSets = [data1, data2, data3]
    } else {
        dataSets = [data1, data2]
    }

    handleCreateChart();        
    
    document.getElementById('dashboard-details').setAttribute('open','');
}


const toggleMinChart = (myChart) => {
    if (chartState === 0) return;
    if (!showMin) {
        myChart.data.datasets.pop();
        if (avalancheMonths.length > snowballMonths.length) {
            myChart.data.labels = avalancheMonths;
        } else {
            myChart.data.labels = snowballMonths;
        }
        myChart.update();
    } else {
        myChart.data.datasets.push(data3);
        myChart.data.labels = minMonths;
        myChart.update();
    }
}

let darkMode = true;

const toggleTheme = () => {
    const htmlElement = document.querySelector('html');
    const toggleBtn = document.querySelector('.theme-btn');
    if (darkMode) {
        htmlElement.setAttribute('data-theme',"dark")
        darkMode = false; 
        toggleBtn.innerHTML = "light_mode"
    } else if (!darkMode) {
        htmlElement.setAttribute('data-theme', "light")
        darkMode = true;
        toggleBtn.innerHTML = "dark_mode"
    }
}


</script>

<body class="container"> 
    <button class="material-symbols-outlined theme-btn contrast" on:click={toggleTheme}>
        dark_mode
    </button>
   
    
    <main> 
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
            <div class="grid" style="margin-bottom: 2rem;">
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
                <div>
                    <label for="min-switch" class="min-switch">
                        <input type="checkbox" id="min-switch" name="min-switch" role="switch" on:change={()=> {showMin = !showMin
                        toggleMinChart(myChart)}}>
                        Show Minimum Payments
                    </label>
                    <button type="submit" form="myForm">Calculate</button>  
                </div>
            </div>
            <details id="dashboard-details" open>
                <summary>Dashboard</summary>
                <Dashboard avalancheData ={avalancheData} snowballData = {snowballData} minPaymentData = {minPaymentData} showMin = {showMin}/>
            </details>
            <details>
                <summary class="secondary">Chart</summary>
                <article class= "chart-container">
                    <div style="width: 800px;"><canvas id="myChart"></canvas></div>
                </article>
            </details>
        </section> 
        
        <section>
        </section>
    </main>
    <footer>
        <Footer />
    </footer>
</body>


<style>
    h3, h1 {
        text-align: center;
    }
    
    .min-switch {
        font-size: .85rem;
    }
    .theme-btn {
        display: flex;
        justify-content: center;
        align-items: center;
        position: fixed;
        width: 50px;
        height: 50px;
        right: 30px;
        top: 20px;
        border-radius: 50%;
        font-size: 1.5rem;
    }
    .chart-container {
        display: flex; 
        justify-content: center;
        align-items: center;
    }
</style>






