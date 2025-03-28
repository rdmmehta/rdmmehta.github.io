---
layout: page
title: EMI Calculator
permalink: /emi-calculator
comments: false
image: images/avatar.jpg
imageshadow: true
---

<div id="emi-calculator" class="emi-container">
    <div class="calculator-grid">
        <div class="input-section">
            <h2 class="calculator-title">EMI Calculator</h2>
            
            <div class="input-group">
                <label for="loan-amount">Loan Amount (₹)</label>
                <input type="number" id="loan-amount" placeholder="e.g. 1,00,000" class="input-field">
            </div>
            
            <div class="input-group">
                <label for="interest-rate">Interest Rate (% p.a.)</label>
                <input type="number" id="interest-rate" step="0.01" placeholder="e.g. 8.5" class="input-field">
            </div>
            
            <div class="input-group">
                <label for="loan-tenure">Loan Tenure (months)</label>
                <input type="number" id="loan-tenure" placeholder="e.g. 60" class="input-field">
            </div>
            
            <div class="input-group">
                <label for="emi-increase">Yearly EMI Increase (optional)</label>
                <input type="number" id="emi-increase" placeholder="e.g. 500" value="0" class="input-field">
            </div>
            
            <div class="input-group">
                <label for="annual-payment">Annual Lump Sum (optional)</label>
                <input type="number" id="annual-payment" placeholder="e.g. 10,000" value="0" class="input-field">
            </div>
            
            <button id="calculate-btn" class="calculate-button">Calculate EMI</button>
            
            <div class="results-container">
                <div class="result-item"><span class="result-label">Monthly EMI:</span> <span id="emi-result" class="result-value">-</span></div>
                <div class="result-item"><span class="result-label">Total Interest:</span> <span id="total-interest" class="result-value">-</span></div>
                <div class="result-item"><span class="result-label">Total Payment:</span> <span id="total-payment" class="result-value">-</span></div>
                <div class="result-item"><span class="result-label">Actual Tenure:</span> <span id="actual-tenure" class="result-value">-</span></div>
            </div>
        </div>
        
        <div class="visualization-section">
            <h3 class="section-title">Payment Composition</h3>
            <div class="chart-container">
                <canvas id="pie-chart"></canvas>
            </div>
            <div id="pie-legend" class="chart-legend"></div>
        </div>
    </div>
    
    <div class="schedule-section">
        <div class="schedule-header">
            <h3 class="section-title">Payment Schedule</h3>
            <button id="export-pdf" class="export-button">Export to PDF</button>
        </div>
        <div class="table-container">
            <table id="schedule-table" class="schedule-table">
                <thead>
                    <tr>
                        <th>Month</th>
                        <th>Principal (₹)</th>
                        <th>Interest (₹)</th>
                        <th>EMI (₹)</th>
                        <th>Balance (₹)</th>
                    </tr>
                </thead>
                <tbody id="schedule-body">
                    <!-- Payment schedule will be inserted here -->
                </tbody>
            </table>
        </div>
    </div>
</div>

<!-- Include libraries -->
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.28/jspdf.plugin.autotable.min.js"></script>

<style>
:root {
    --primary-color: #4361ee;
    --secondary-color: #3f37c9;
    --accent-color: #4895ef;
    --light-color: #f8f9fa;
    --dark-color: #212529;
    --success-color: #4cc9f0;
    --danger-color: #f72585;
    --warning-color: #f8961e;
    --border-radius: 8px;
    --box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    --transition: all 0.3s ease;
}

* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.emi-container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 20px;
    color: var(--dark-color);
}

.calculator-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
    gap: 30px;
    margin-bottom: 30px;
}

.calculator-title {
    color: var(--primary-color);
    margin-bottom: 20px;
    font-size: 1.8rem;
}

.section-title {
    color: var(--secondary-color);
    margin-bottom: 15px;
    font-size: 1.4rem;
}

.input-group {
    margin-bottom: 15px;
}

.input-group label {
    display: block;
    margin-bottom: 5px;
    font-weight: 500;
    color: var(--dark-color);
}

.input-field {
    width: 100%;
    padding: 12px;
    border: 1px solid #ddd;
    border-radius: var(--border-radius);
    font-size: 1rem;
    transition: var(--transition);
}

.input-field:focus {
    outline: none;
    border-color: var(--primary-color);
    box-shadow: 0 0 0 2px rgba(67, 97, 238, 0.2);
}

.calculate-button {
    width: 100%;
    padding: 12px;
    background-color: var(--primary-color);
    color: white;
    border: none;
    border-radius: var(--border-radius);
    font-size: 1rem;
    font-weight: 600;
    cursor: pointer;
    transition: var(--transition);
    margin-top: 10px;
}

.calculate-button:hover {
    background-color: var(--secondary-color);
    box-shadow: var(--box-shadow);
}

.results-container {
    background-color: var(--light-color);
    padding: 20px;
    border-radius: var(--border-radius);
    margin-top: 20px;
}

.result-item {
    display: flex;
    justify-content: space-between;
    margin-bottom: 10px;
    padding-bottom: 10px;
    border-bottom: 1px solid #eee;
}

.result-item:last-child {
    border-bottom: none;
    margin-bottom: 0;
}

.result-label {
    font-weight: 500;
}

.result-value {
    font-weight: 600;
    color: var(--primary-color);
}

.visualization-section {
    background-color: white;
    padding: 20px;
    border-radius: var(--border-radius);
    box-shadow: var(--box-shadow);
    display: flex;
    flex-direction: column;
}

.chart-container {
    position: relative;
    height: 250px;
    width: 100%;
    margin: 0 auto;
}

.chart-legend {
    display: flex;
    justify-content: center;
    gap: 20px;
    margin-top: 15px;
    flex-wrap: wrap;
}

.chart-legend div {
    display: flex;
    align-items: center;
    font-size: 0.9rem;
}

.chart-legend span {
    display: inline-block;
    width: 12px;
    height: 12px;
    margin-right: 5px;
    border-radius: 2px;
}

.schedule-section {
    background-color: white;
    padding: 20px;
    border-radius: var(--border-radius);
    box-shadow: var(--box-shadow);
}

.schedule-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 15px;
    flex-wrap: wrap;
    gap: 10px;
}

.export-button {
    padding: 8px 16px;
    background-color: var(--danger-color);
    color: white;
    border: none;
    border-radius: var(--border-radius);
    font-size: 0.9rem;
    font-weight: 500;
    cursor: pointer;
    transition: var(--transition);
    display: none;
}

.export-button:hover {
    background-color: #d11a6b;
    box-shadow: var(--box-shadow);
}

.table-container {
    overflow-x: auto;
}

.schedule-table {
    width: 100%;
    border-collapse: collapse;
    font-size: 0.9rem;
}

.schedule-table th {
    background-color: var(--primary-color);
    color: white;
    padding: 12px;
    text-align: left;
}

.schedule-table td {
    padding: 10px 12px;
    border-bottom: 1px solid #eee;
}

.schedule-table tr:nth-child(even) {
    background-color: var(--light-color);
}

.schedule-table tr:hover {
    background-color: #e9ecef;
}

/* Responsive adjustments */
@media (max-width: 768px) {
    .calculator-grid {
        grid-template-columns: 1fr;
    }
    
    .calculator-title {
        font-size: 1.5rem;
    }
    
    .section-title {
        font-size: 1.2rem;
    }
    
    .input-field, .calculate-button {
        padding: 10px;
    }
    
    .schedule-table {
        font-size: 0.8rem;
    }
    
    .schedule-table th, .schedule-table td {
        padding: 8px 10px;
    }
}

@media (max-width: 480px) {
    .emi-container {
        padding: 15px;
    }
    
    .chart-legend {
        flex-direction: column;
        gap: 8px;
        align-items: flex-start;
    }
    
    .schedule-header {
        flex-direction: column;
        align-items: flex-start;
    }
    
    .export-button {
        width: 100%;
    }
}
</style>

<script>
// Initialize jsPDF
const { jsPDF } = window.jspdf;

// Format Indian currency with ₹ symbol and commas
function formatINR(amount) {
    return '₹' + amount.toFixed(2).replace(/\d(?=(\d{2})+\d\.)/g, '$&,');
}

// Variables for chart
let pieChart;

document.getElementById('calculate-btn').addEventListener('click', function() {
    // Get input values
    const loanAmount = parseFloat(document.getElementById('loan-amount').value);
    const interestRate = parseFloat(document.getElementById('interest-rate').value) / 100 / 12; // monthly rate
    const loanTenure = parseInt(document.getElementById('loan-tenure').value);
    const emiIncrease = parseFloat(document.getElementById('emi-increase').value) || 0;
    const annualPayment = parseFloat(document.getElementById('annual-payment').value) || 0;
    
    // Validate inputs
    if (isNaN(loanAmount) || isNaN(interestRate) || isNaN(loanTenure)) {
        alert('Please enter valid values for Loan Amount, Interest Rate, and Loan Tenure');
        return;
    }
    
    // Calculate initial EMI
    const emi = calculateEMI(loanAmount, interestRate, loanTenure);
    let principal = loanAmount;
    let totalInterest = 0;
    let actualMonths = 0;
    let currentEMI = emi;
    let schedule = [];
    
    // Generate payment schedule
    for (let month = 1; month <= loanTenure && principal > 0; month++) {
        const interest = principal * interestRate;
        let principalPaid = currentEMI - interest;
        
        // Adjust for final payment
        if (principalPaid > principal) {
            principalPaid = principal;
            currentEMI = principalPaid + interest;
        }
        
        // Check if this is the last month of the year for annual payment
        let annualPaymentThisMonth = 0;
        if (annualPayment > 0 && month % 12 === 0 && month !== loanTenure) {
            annualPaymentThisMonth = Math.min(annualPayment, principal);
            principalPaid += annualPaymentThisMonth;
            currentEMI += annualPaymentThisMonth;
        }
        
        principal -= principalPaid;
        totalInterest += interest;
        actualMonths = month;
        
        schedule.push({
            month,
            principal: principalPaid - annualPaymentThisMonth,
            interest,
            emi: currentEMI - annualPaymentThisMonth,
            annualPayment: annualPaymentThisMonth,
            balance: principal
        });
        
        // Increase EMI at the start of each year (after 12 months)
        if (emiIncrease > 0 && month % 12 === 0) {
            currentEMI += emiIncrease;
        }
    }
    
    // Display results with INR formatting
    document.getElementById('emi-result').textContent = formatINR(emi);
    document.getElementById('total-interest').textContent = formatINR(totalInterest);
    document.getElementById('total-payment').textContent = formatINR(loanAmount + totalInterest);
    document.getElementById('actual-tenure').textContent = `${actualMonths} months (${Math.ceil(actualMonths/12)} years)`;
    
    // Update pie chart
    updatePieChart(loanAmount, totalInterest);
    
    // Display payment schedule
    const scheduleBody = document.getElementById('schedule-body');
    scheduleBody.innerHTML = '';
    
    schedule.forEach(payment => {
        const row = document.createElement('tr');
        
        row.innerHTML = `
            <td>${payment.month}</td>
            <td>${formatINR(payment.principal)}</td>
            <td>${formatINR(payment.interest)}</td>
            <td>${formatINR(payment.emi)}${payment.annualPayment > 0 ? ` (+${formatINR(payment.annualPayment)})` : ''}</td>
            <td>${formatINR(payment.balance)}</td>
        `;
        
        scheduleBody.appendChild(row);
    });
    
    // Show export button
    document.getElementById('export-pdf').style.display = 'block';
    
    // Store schedule data for PDF export
    document.getElementById('export-pdf').onclick = function() {
        exportToPDF(schedule, loanAmount, interestRate * 12 * 100, emi, totalInterest, actualMonths);
    };
});

function calculateEMI(principal, monthlyRate, months) {
    return principal * monthlyRate * Math.pow(1 + monthlyRate, months) / (Math.pow(1 + monthlyRate, months) - 1);
}

function updatePieChart(principal, totalInterest) {
    const ctx = document.getElementById('pie-chart').getContext('2d');
    
    // Destroy previous chart if exists
    if (pieChart) {
        pieChart.destroy();
    }
    
    pieChart = new Chart(ctx, {
        type: 'doughnut',
        data: {
            labels: ['Principal', 'Interest'],
            datasets: [{
                data: [principal, totalInterest],
                backgroundColor: ['#4361ee', '#f72585'],
                borderWidth: 0,
                borderRadius: 6
            }]
        },
        options: {
            responsive: true,
            maintainAspectRatio: false,
            cutout: '65%',
            plugins: {
                legend: {
                    display: false
                },
                tooltip: {
                    callbacks: {
                        label: function(context) {
                            const label = context.label || '';
                            const value = context.raw || 0;
                            const total = context.dataset.data.reduce((a, b) => a + b, 0);
                            const percentage = Math.round((value / total) * 100);
                            return `${label}: ${formatINR(value)} (${percentage}%)`;
                        }
                    }
                }
            }
        }
    });
    
    // Update legend with actual values
    const legendHtml = `
        <div>
            <div><span style="background-color: #4361ee;"></span> 
            Principal: ${formatINR(principal)}</div>
            <div><span style="background-color: #f72585;"></span> 
            Interest: ${formatINR(totalInterest)}</div>
        </div>
    `;
    document.getElementById('pie-legend').innerHTML = legendHtml;
}

function exportToPDF(schedule, loanAmount, annualInterestRate, emi, totalInterest, actualMonths) {
    const doc = new jsPDF();
    
    // Add logo and header
    doc.setFontSize(10);
    doc.setTextColor(100);
    doc.text('Generated by: Your Company Name', 14, 10);
    doc.text('Date: ' + new Date().toLocaleDateString(), 180, 10, { align: 'right' });
    
    // Add title
    doc.setFontSize(18);
    doc.setTextColor(40);
    doc.setFont('helvetica', 'bold');
    doc.text('Loan EMI Payment Schedule', 105, 25, { align: 'center' });
    
    // Add divider line
    doc.setDrawColor(200);
    doc.setLineWidth(0.5);
    doc.line(14, 30, 196, 30);
    
    // Add summary section
    doc.setFontSize(12);
    doc.setTextColor(60);
    doc.setFont('helvetica', 'normal');
    
    const summaryYStart = 40;
    const col1 = 14;
    const col2 = 60;
    const col3 = 120;
    const col4 = 180;
    
    // Summary table headers
    doc.setFillColor(240, 240, 240);
    doc.rect(col1, summaryYStart, 180, 8, 'F');
    doc.setFont('helvetica', 'bold');
    doc.text('Loan Summary', 105, summaryYStart + 6, { align: 'center' });
    
    // Summary content
    doc.setFont('helvetica', 'normal');
    let y = summaryYStart + 16;
    
    doc.text('Loan Amount:', col1, y);
    doc.text(formatINR(loanAmount), col2, y);
    
    doc.text('Interest Rate:', col3, y);
    doc.text(annualInterestRate.toFixed(2) + '% p.a.', col4, y);
    y += 8;
    
    doc.text('Monthly EMI:', col1, y);
    doc.text(formatINR(emi), col2, y);
    
    doc.text('Loan Tenure:', col3, y);
    doc.text(`${actualMonths} months (${Math.ceil(actualMonths/12)} years)`, col4, y);
    y += 8;
    
    doc.text('Total Interest:', col1, y);
    doc.text(formatINR(totalInterest), col2, y);
    
    doc.text('Total Payment:', col3, y);
    doc.text(formatINR(loanAmount + totalInterest), col4, y);
    y += 12;
    
    // Add payment composition section
    doc.setFont('helvetica', 'bold');
    doc.text('Payment Composition', 105, y, { align: 'center' });
    y += 10;
    
    // Simple representation of pie chart data
    doc.setFontSize(10);
    doc.setFillColor(67, 97, 238);
    doc.rect(80, y, 10, 10, 'F');
    doc.setTextColor(0, 0, 0);
    doc.text('Principal: ' + formatINR(loanAmount), 95, y + 8);
    
    doc.setFillColor(247, 37, 133);
    doc.rect(80, y + 15, 10, 10, 'F');
    doc.text('Interest: ' + formatINR(totalInterest), 95, y + 23);
    y += 30;
    
    // Add payment schedule section header
    doc.setFillColor(240, 240, 240);
    doc.rect(col1, y, 180, 8, 'F');
    doc.setFont('helvetica', 'bold');
    doc.text('Payment Schedule', 105, y + 6, { align: 'center' });
    y += 10;
    
    // Prepare data for the table with INR formatting
    const tableData = schedule.map(payment => [
        payment.month,
        formatINR(payment.principal),
        formatINR(payment.interest),
        payment.annualPayment > 0 
            ? `${formatINR(payment.emi + payment.annualPayment)}\n(+${formatINR(payment.annualPayment)})`
            : formatINR(payment.emi),
        formatINR(payment.balance)
    ]);
    
    // Add the payment schedule table
    doc.autoTable({
        startY: y,
        head: [['Month', 'Principal (₹)', 'Interest (₹)', 'EMI (₹)', 'Balance (₹)']],
        body: tableData,
        margin: { top: y },
        styles: { 
            fontSize: 8,
            cellPadding: 3,
            overflow: 'linebreak'
        },
        headStyles: {
            fillColor: [67, 97, 238],
            textColor: [255, 255, 255],
            fontStyle: 'bold',
            fontSize: 9
        },
        alternateRowStyles: {
            fillColor: [245, 245, 245]
        },
        columnStyles: {
            0: { cellWidth: 15, halign: 'center' },
            1: { cellWidth: 30, halign: 'right' },
            2: { cellWidth: 30, halign: 'right' },
            3: { cellWidth: 45, halign: 'right' },
            4: { cellWidth: 30, halign: 'right' }
        },
        theme: 'grid',
        tableLineColor: [220, 220, 220],
        tableLineWidth: 0.1
    });
    
    // Add footer
    const pageCount = doc.internal.getNumberOfPages();
    for(let i = 1; i <= pageCount; i++) {
        doc.setPage(i);
        doc.setFontSize(8);
        doc.setTextColor(100);
        doc.text(`Page ${i} of ${pageCount}`, 105, 285, { align: 'center' });
        doc.text('Confidential - For customer use only', 105, 290, { align: 'center' });
    }
    
    // Save the PDF
    doc.save(`EMI_Schedule_${new Date().toISOString().slice(0,10)}.pdf`);
}
</script>
