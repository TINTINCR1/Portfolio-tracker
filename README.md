    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title># My Portfolio-Tracker A simple portfolio tracker built with HTML, CSS, and JavaScript.</title>
        <!-- Tailwind CSS from CDN for modern, responsive styling -->
        <script src="https://cdn.tailwindcss.com"></script>
        <!-- Chart.js library for creating charts -->
        <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
        <style>
            /* Custom styles for a dark, clean look */
            body {
                font-family: 'Inter', sans-serif;
                background-color: #1a202c; /* Dark background */
                color: #e2e8f0; /* Light text */
            }
            canvas {
                max-height: 400px; /* Limit canvas height for better mobile viewing */
            }
        </style>
    </head>
    <body class="p-4 sm:p-8 flex flex-col items-center justify-center min-h-screen">

        <div class="w-full max-w-5xl p-6 bg-gray-800 rounded-2xl shadow-lg border border-gray-700">
            
            <!-- Header -->
            <div class="flex flex-col sm:flex-row items-center justify-center mb-6 text-center">
                <h1 class="text-3xl font-extrabold text-white">Simple Portfolio Tracker (Static)</h1>
            </div>
            
            <!-- Portfolio Table -->
            <div class="overflow-x-auto rounded-xl border border-gray-700 mb-8">
                <table class="min-w-full bg-gray-900 rounded-xl">
                    <thead>
                        <tr class="bg-gray-700 text-gray-300 uppercase text-xs sm:text-sm leading-normal">
                            <th class="py-3 px-3 sm:px-6 text-left rounded-tl-xl">Ticker</th>
                            <th class="py-3 px-3 sm:px-6 text-left">Security Type</th>
                            <th class="py-3 px-3 sm:px-6 text-left">Price</th>
                            <th class="py-3 px-3 sm:px-6 text-left">Shares</th>
                            <th class="py-3 px-3 sm:px-6 text-left">Value</th>
                            <th class="py-3 px-3 sm:px-6 text-left">Purchase Price</th>
                            <th class="py-3 px-3 sm:px-6 text-left">Cost Basis</th>
                            <th class="py-3 px-3 sm:px-6 text-left rounded-tr-xl">Total Return %</th>
                        </tr>
                    </thead>
                    <tbody id="portfolio-body" class="text-gray-200 text-xs sm:text-sm font-light">
                        <!-- Table rows will be populated by JavaScript -->
                    </tbody>
                </table>
            </div>


            <!-- Charts Section -->
            <div class="flex flex-col md:flex-row gap-8" id="charts-section">
                <!-- Bar Chart for Total Returns -->
                <div class="w-full md:w-1/2 p-4 bg-gray-900 rounded-2xl border border-gray-700">
                    <h2 class="text-xl font-bold text-white mb-4 text-center">My Returns</h2>
                    <canvas id="barChart"></canvas>
                </div>
                
                <!-- Pie Chart for Portfolio Allocation -->
                <div class="w-full md:w-1/2 p-4 bg-gray-900 rounded-2xl border border-gray-700">
                    <h2 class="text-xl font-bold text-white mb-4 text-center">Portfolio Allocation</h2>
                    <canvas id="pieChart"></canvas>
                </div>
            </div>
        </div>

        <script>
            // --- Core JavaScript Logic for Static Charts ---
            
            // Wait for the window to load before running the script
            window.onload = function() {
                // Hardcoded data based on the provided screenshot
                const portfolioData = [
                    { ticker: 'AAPL', type: 'Stocks', price: 227.16, shares: 1, value: 227.16, purchasePrice: 155.00, costBasis: 155.00, return: 46.55 },
                    { ticker: 'MSFT', type: 'Stocks', price: 504.26, shares: 1, value: 504.26, purchasePrice: 224.00, costBasis: 224.00, return: 125.12 },
                    { ticker: 'MMM', type: 'Stocks', price: 155.85, shares: 1, value: 155.85, purchasePrice: 115.00, costBasis: 115.00, return: 35.52 },
                    { ticker: 'PG', type: 'Stocks', price: 156.15, shares: 1, value: 156.15, purchasePrice: 143.00, costBasis: 143.00, return: 9.20 },
                    { ticker: 'SPY', type: 'ETFs', price: 642.47, shares: 1, value: 642.47, purchasePrice: 421.00, costBasis: 421.00, return: 52.61 },
                    { ticker: 'QQQ', type: 'ETFs', price: 570.32, shares: 1, value: 570.32, purchasePrice: 380.00, costBasis: 380.00, return: 50.08 },
                    { ticker: 'VTI', type: 'ETFs', price: 316.65, shares: 1, value: 316.65, purchasePrice: 180.00, costBasis: 180.00, return: 75.92 },
                    { ticker: 'VXUS', type: 'Internat...', price: 71.65, shares: 1, value: 71.65, purchasePrice: 53.00, costBasis: 53.00, return: 35.19 },
                    { ticker: 'BTCUSD', type: 'Crypto', price: 109825.80, shares: 0.1, value: 10982.58, purchasePrice: 56000.00, costBasis: 5600.00, return: 96.12 },
                    { ticker: 'TLT', type: 'Bonds', price: 86.80, shares: 1, value: 86.80, purchasePrice: 55.00, costBasis: 55.00, return: 57.82 },
                    { ticker: 'MD', type: 'Stocks', price: 17.06, shares: 1, value: 17.06, purchasePrice: 350.00, costBasis: 350.00, return: -95.13 },
                    { ticker: 'SCHD', type: 'ETFs', price: 27.77, shares: 1, value: 27.77, purchasePrice: 23.00, costBasis: 23.00, return: 20.74 }
                ];
                
                // Function to render the portfolio table
                function renderPortfolioTable(data) {
                    const portfolioBody = document.getElementById('portfolio-body');
                    portfolioBody.innerHTML = ''; // Clear existing content

                    data.forEach(item => {
                        const row = document.createElement('tr');
                        row.classList.add('hover:bg-gray-800', 'border-b', 'border-gray-700');
                        const returnTextColor = item.return >= 0 ? 'text-green-400' : 'text-red-400';
                        const sharesDisplay = item.shares.toFixed(2);
                        const valueDisplay = item.value.toFixed(2);
                        const costBasisDisplay = item.costBasis.toFixed(2);

                        row.innerHTML = `
                            <td class="py-3 px-3 sm:px-6 text-left whitespace-nowrap">${item.ticker}</td>
                            <td class="py-3 px-3 sm:px-6 text-left">${item.type}</td>
                            <td class="py-3 px-3 sm:px-6 text-left">$${item.price.toFixed(2)}</td>
                            <td class="py-3 px-3 sm:px-6 text-left">${sharesDisplay}</td>
                            <td class="py-3 px-3 sm:px-6 text-left">$${valueDisplay}</td>
                            <td class="py-3 px-3 sm:px-6 text-left">$${item.purchasePrice.toFixed(2)}</td>
                            <td class="py-3 px-3 sm:px-6 text-left">$${costBasisDisplay}</td>
                            <td class="py-3 px-3 sm:px-6 text-left font-semibold ${returnTextColor}">${item.return.toFixed(2)}%</td>
                        `;
                        portfolioBody.appendChild(row);
                    });
                }

                // Function to create the Bar Chart for Returns
                function createBarChart(data) {
                    const ctx = document.getElementById('barChart').getContext('2d');
                    const labels = data.map(item => item.ticker);
                    const returns = data.map(item => item.return);
                    // Color bars based on positive or negative return
                    const backgroundColors = returns.map(value => value >= 0 ? '#48bb78' : '#e53e3e');

                    new Chart(ctx, {
                        type: 'bar',
                        data: {
                            labels: labels,
                            datasets: [{
                                label: 'Total Return %',
                                data: returns,
                                backgroundColor: backgroundColors
                            }]
                        },
                        options: {
                            indexAxis: 'y', // Make the bars horizontal
                            responsive: true,
                            plugins: {
                                legend: {
                                    display: false
                                },
                                title: {
                                    display: false
                                }
                            },
                            scales: {
                                x: {
                                    ticks: { color: '#e2e8f0' },
                                    grid: { color: 'rgba(255,255,255,0.1)' }
                                },
                                y: {
                                    ticks: { color: '#e2e8f0' },
                                    grid: { color: 'rgba(255,255,255,0.1)' }
                                }
                            }
                        }
                    });
                }

                // Function to create the Pie Chart for Allocation
                function createPieChart(data) {
                    const ctx = document.getElementById('pieChart').getContext('2d');
                    const labels = data.map(item => item.ticker);
                    const values = data.map(item => item.value);
                    const backgroundColors = [
                        '#4299e1', '#667eea', '#9f7aea', '#d53f8c', '#ed64a6',
                        '#f6ad55', '#48bb78', '#38b2ac', '#4fd1c5', '#5a67d8',
                        '#dd6b20', '#ecc94b'
                    ];

                    new Chart(ctx, {
                        type: 'pie',
                        data: {
                            labels: labels,
                            datasets: [{
                                data: values,
                                backgroundColor: backgroundColors,
                                hoverOffset: 4
                            }]
                        },
                        options: {
                            responsive: true,
                            plugins: {
                                legend: {
                                    position: 'right',
                                    labels: {
                                        color: '#e2e8f0'
                                    }
                                }
                            }
                        }
                    });
                }

                // Call the functions to create the charts
                renderPortfolioTable(portfolioData);
                createBarChart(portfolioData);
                createPieChart(portfolioData);
            };
        </script>
    </body>
    </html>
