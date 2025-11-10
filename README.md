<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>LEI Entity Lookup Tool - Shareable Version</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            background: white;
            border-radius: 15px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
            overflow: hidden;
        }

        .header {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 30px;
            text-align: center;
        }

        .header h1 {
            font-size: 2.5em;
            margin-bottom: 10px;
            text-shadow: 0 2px 4px rgba(0,0,0,0.3);
        }

        .header p {
            font-size: 1.1em;
            opacity: 0.9;
        }

        .main-content {
            padding: 30px;
        }

        .input-section {
            margin-bottom: 30px;
        }

        .input-section h3 {
            color: #333;
            margin-bottom: 15px;
            font-size: 1.3em;
            display: flex;
            align-items: center;
        }

        .input-section h3::before {
            content: "📝";
            margin-right: 10px;
        }

        textarea {
            width: 100%;
            height: 150px;
            padding: 15px;
            border: 2px solid #e0e0e0;
            border-radius: 8px;
            font-size: 14px;
            font-family: 'Courier New', monospace;
            resize: vertical;
            transition: border-color 0.3s, box-shadow 0.3s;
            background: #fafafa;
        }

        textarea:focus {
            outline: none;
            border-color: #667eea;
            box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
            background: white;
        }

        .button-group {
            display: flex;
            gap: 15px;
            margin: 20px 0;
            flex-wrap: wrap;
        }

        button {
            padding: 12px 24px;
            border: none;
            border-radius: 8px;
            font-size: 16px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s;
            min-width: 140px;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
        }

        .btn-primary {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
        }

        .btn-primary:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
        }

        .btn-secondary {
            background: linear-gradient(135deg, #28a745 0%, #20c997 100%);
            color: white;
        }

        .btn-secondary:hover {
            background: linear-gradient(135deg, #218838 0%, #1ea986 100%);
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(40, 167, 69, 0.4);
        }

        .btn-tertiary {
            background: linear-gradient(135deg, #17a2b8 0%, #138496 100%);
            color: white;
        }

        .btn-tertiary:hover {
            background: linear-gradient(135deg, #138496 0%, #0f6674 100%);
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(23, 162, 184, 0.4);
        }

        .btn-clear {
            background: linear-gradient(135deg, #dc3545 0%, #c82333 100%);
            color: white;
        }

        .btn-clear:hover {
            background: linear-gradient(135deg, #c82333 0%, #a71e2a 100%);
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(220, 53, 69, 0.4);
        }

        button:disabled {
            background: #6c757d !important;
            cursor: not-allowed;
            transform: none !important;
            box-shadow: none !important;
            opacity: 0.7;
        }

        .progress-container {
            margin: 20px 0;
            display: none;
        }

        .progress-bar {
            width: 100%;
            height: 25px;
            background: #e9ecef;
            border-radius: 12px;
            overflow: hidden;
            box-shadow: inset 0 1px 3px rgba(0,0,0,0.2);
        }

        .progress-fill {
            height: 100%;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            width: 0%;
            transition: width 0.3s;
            border-radius: 12px;
            position: relative;
        }

        .progress-fill::after {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: linear-gradient(45deg, 
                rgba(255,255,255,0.2) 25%, 
                transparent 25%, 
                transparent 50%, 
                rgba(255,255,255,0.2) 50%, 
                rgba(255,255,255,0.2) 75%, 
                transparent 75%);
            background-size: 20px 20px;
            animation: progress-animation 1s linear infinite;
        }

        @keyframes progress-animation {
            0% { background-position: 0 0; }
            100% { background-position: 20px 0; }
        }

        .progress-text {
            text-align: center;
            margin-top: 10px;
            color: #666;
            font-weight: 600;
        }

        .results-section {
            margin-top: 30px;
        }

        .results-section h3 {
            color: #333;
            margin-bottom: 20px;
            font-size: 1.3em;
            display: flex;
            align-items: center;
        }

        .results-section h3::before {
            content: "📊";
            margin-right: 10px;
        }

        .results-table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
            background: white;
            border-radius: 8px;
            overflow: hidden;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
        }

        .results-table th,
        .results-table td {
            padding: 15px;
            text-align: left;
            border-bottom: 1px solid #e9ecef;
        }

        .results-table th {
            background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
            font-weight: 700;
            color: #495057;
            font-size: 0.9em;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        .results-table tbody tr {
            transition: background-color 0.2s;
        }

        .results-table tbody tr:hover {
            background: #f8f9fa;
        }

        .results-table tbody tr:nth-child(even) {
            background: rgba(102, 126, 234, 0.02);
        }

        .status-success {
            color: #28a745;
            font-weight: 700;
            display: flex;
            align-items: center;
        }

        .status-success::before {
            content: "✅";
            margin-right: 5px;
        }

        .status-error {
            color: #dc3545;
            font-weight: 700;
            display: flex;
            align-items: center;
        }

        .status-error::before {
            content: "❌";
            margin-right: 5px;
        }

        .lei-code {
            font-family: 'Courier New', monospace;
            font-weight: 600;
            color: #495057;
            background: rgba(102, 126, 234, 0.1);
            padding: 4px 8px;
            border-radius: 4px;
            font-size: 0.9em;
        }

        .stats {
            display: flex;
            gap: 20px;
            margin: 20px 0;
            flex-wrap: wrap;
        }

        .stat-card {
            background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
            padding: 20px;
            border-radius: 12px;
            border-left: 5px solid #667eea;
            flex: 1;
            min-width: 180px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            transition: transform 0.2s;
        }

        .stat-card:hover {
            transform: translateY(-2px);
        }

        .stat-value {
            font-size: 2em;
            font-weight: 700;
            color: #333;
            margin-bottom: 5px;
        }

        .stat-label {
            color: #666;
            font-size: 0.9em;
            font-weight: 600;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        .alert {
            padding: 20px;
            border-radius: 12px;
            margin: 15px 0;
            border-left: 5px solid;
        }

        .alert-info {
            background: linear-gradient(135deg, #d1ecf1 0%, #b8daff 100%);
            border-left-color: #17a2b8;
            color: #0c5460;
        }

        .alert-warning {
            background: linear-gradient(135deg, #fff3cd 0%, #ffeaa7 100%);
            border-left-color: #ffc107;
            color: #856404;
        }

        .alert strong {
            display: flex;
            align-items: center;
            margin-bottom: 10px;
        }

        .alert strong::before {
            content: "💡";
            margin-right: 8px;
        }

        .footer-info {
            margin-top: 30px;
            padding: 20px;
            background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
            border-radius: 12px;
            text-align: center;
            color: #666;
        }

        .footer-info p {
            margin: 5px 0;
        }

        .footer-info a {
            color: #667eea;
            text-decoration: none;
        }

        .footer-info a:hover {
            text-decoration: underline;
        }

        @media (max-width: 768px) {
            .container {
                margin: 10px;
                border-radius: 12px;
            }

            .main-content {
                padding: 20px;
            }

            .button-group {
                flex-direction: column;
            }

            button {
                width: 100%;
                min-width: auto;
            }

            .stats {
                flex-direction: column;
            }

            .header h1 {
                font-size: 2em;
            }

            .header {
                padding: 20px;
            }

            .results-table {
                font-size: 0.9em;
            }

            .results-table th,
            .results-table td {
                padding: 10px 8px;
            }
        }

        /* Loading animation */
        .loading-spinner {
            display: none;
            width: 20px;
            height: 20px;
            border: 2px solid rgba(255,255,255,0.3);
            border-radius: 50%;
            border-top-color: white;
            animation: spin 1s ease-in-out infinite;
        }

        @keyframes spin {
            to { transform: rotate(360deg); }
        }

        /* Tooltip */
        .tooltip {
            position: relative;
            cursor: help;
        }

        .tooltip .tooltiptext {
            visibility: hidden;
            width: 200px;
            background-color: #555;
            color: #fff;
            text-align: center;
            border-radius: 6px;
            padding: 5px;
            position: absolute;
            z-index: 1;
            bottom: 125%;
            left: 50%;
            margin-left: -100px;
            opacity: 0;
            transition: opacity 0.3s;
            font-size: 0.8em;
        }

        .tooltip:hover .tooltiptext {
            visibility: visible;
            opacity: 1;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🔍 LEI Entity Lookup Tool</h1>
            <p>Professional LEI code lookup with GLEIF API integration</p>
        </div>

        <div class="main-content">
            <div class="input-section">
                <h3>Enter LEI Codes</h3>
                <textarea 
                    id="leiInput" 
                    placeholder="Paste LEI codes here (one per line)&#10;&#10;Example:&#10;213800MBWEIBK649V516&#10;5493000IBP32UQZ0KL24&#10;549300E9PC51EN656011&#10;&#10;💡 Tip: Use Ctrl+Enter to start lookup"
                ></textarea>
                
                <div class="button-group">
                    <button id="lookupBtn" class="btn-primary" onclick="lookupLEIs()">
                        <span id="lookup-text">🔍 Lookup Entities</span>
                        <div class="loading-spinner" id="loading-spinner"></div>
                    </button>
                    <button id="exportCsvBtn" class="btn-secondary" onclick="exportToCSV()" disabled>
                        📊 Export CSV
                    </button>
                    <button id="exportExcelBtn" class="btn-tertiary" onclick="exportToExcel()" disabled>
                        📈 Export Excel
                    </button>
                    <button id="clearBtn" class="btn-clear" onclick="clearResults()">
                        🗑️ Clear All
                    </button>
                </div>

                <div class="alert alert-info">
                    <strong>Usage Instructions</strong>
                    <ul style="margin: 10px 0 0 20px; line-height: 1.6;">
                        <li><strong>Input:</strong> Paste LEI codes one per line in the textarea above</li>
                        <li><strong>Validation:</strong> Tool automatically cleans and validates LEI format (20 characters)</li>
                        <li><strong>Processing:</strong> Real-time progress tracking with API rate limiting</li>
                        <li><strong>Export:</strong> Download results in CSV or Excel format</li>
                        <li><strong>Keyboard:</strong> Use Ctrl+Enter for quick lookup</li>
                    </ul>
                </div>
            </div>

            <div class="progress-container" id="progressContainer">
                <div class="progress-bar">
                    <div class="progress-fill" id="progressFill"></div>
                </div>
                <div class="progress-text" id="progressText">Processing LEI codes...</div>
            </div>

            <div class="results-section" id="resultsSection" style="display: none;">
                <h3>Lookup Results</h3>
                
                <div class="stats" id="statsContainer">
                    <div class="stat-card">
                        <div class="stat-value" id="totalCount">0</div>
                        <div class="stat-label">Total LEIs</div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-value" id="successCount" style="color: #28a745;">0</div>
                        <div class="stat-label">Successfully Found</div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-value" id="errorCount" style="color: #dc3545;">0</div>
                        <div class="stat-label">Not Found / Errors</div>
                    </div>
                </div>

                <table class="results-table" id="resultsTable">
                    <thead>
                        <tr>
                            <th>LEI Code</th>
                            <th>Entity Name</th>
                            <th>Country</th>
                            <th>Status</th>
                            <th>Registration Date</th>
                        </tr>
                    </thead>
                    <tbody id="resultsBody">
                    </tbody>
                </table>
            </div>

            <div class="footer-info">
                <p><strong>About this Tool:</strong> Professional LEI Entity Lookup using GLEIF API</p>
                <p>Data source: <a href="https://www.gleif.org/" target="_blank">Global Legal Entity Identifier Foundation (GLEIF)</a></p>
                <p>Tool Version: 2.0 | Last Updated: November 2025</p>
            </div>
        </div>
    </div>

    <script>
        let resultsData = [];

        function cleanLEI(lei) {
            return lei.replace(/[^A-Z0-9]/g, '').toUpperCase();
        }

        function isValidLEI(lei) {
            return lei.length === 20 && /^[A-Z0-9]{20}$/.test(lei);
        }

        function updateButtonLoading(isLoading) {
            const lookupBtn = document.getElementById('lookupBtn');
            const lookupText = document.getElementById('lookup-text');
            const spinner = document.getElementById('loading-spinner');
            
            if (isLoading) {
                lookupText.style.display = 'none';
                spinner.style.display = 'block';
                lookupBtn.disabled = true;
            } else {
                lookupText.style.display = 'block';
                spinner.style.display = 'none';
                lookupBtn.disabled = false;
            }
        }

        async function lookupLEIs() {
            const input = document.getElementById('leiInput').value.trim();
            if (!input) {
                alert('⚠️ Please enter at least one LEI code.');
                return;
            }

            const leis = input.split('\n')
                .map(lei => cleanLEI(lei.trim()))
                .filter(lei => lei.length > 0);

            const validLEIs = leis.filter(isValidLEI);
            const invalidLEIs = leis.filter(lei => !isValidLEI(lei));

            if (invalidLEIs.length > 0) {
                const message = `⚠️ Found ${invalidLEIs.length} invalid LEI code(s):\n\n${invalidLEIs.join('\n')}\n\n✅ Continue with ${validLEIs.length} valid LEI(s)?`;
                if (!confirm(message)) return;
            }

            if (validLEIs.length === 0) {
                alert('❌ No valid LEI codes found. LEI codes must be exactly 20 characters long.');
                return;
            }

            // Reset results
            resultsData = [];
            document.getElementById('resultsSection').style.display = 'none';
            
            // Show progress
            const progressContainer = document.getElementById('progressContainer');
            const progressFill = document.getElementById('progressFill');
            const progressText = document.getElementById('progressText');
            progressContainer.style.display = 'block';
            
            // Update button state
            updateButtonLoading(true);
            document.getElementById('exportCsvBtn').disabled = true;
            document.getElementById('exportExcelBtn').disabled = true;

            let completed = 0;
            const total = validLEIs.length;
            const startTime = Date.now();

            for (const lei of validLEIs) {
                try {
                    const response = await fetch(`https://api.gleif.org/api/v1/lei-records/${lei}`);
                    const progress = ((completed + 1) / total) * 100;
                    progressFill.style.width = `${progress}%`;
                    
                    const elapsed = Date.now() - startTime;
                    const estimated = (elapsed / (completed + 1)) * (total - completed - 1);
                    const estimatedStr = estimated > 1000 ? `${Math.round(estimated/1000)}s remaining` : 'Almost done';
                    
                    progressText.textContent = `Processing ${completed + 1} of ${total} LEI codes... ${estimatedStr}`;

                    if (response.ok) {
                        const data = await response.json();
                        const entity = data.data.attributes.entity;
                        const registration = data.data.attributes.registration;
                        
                        resultsData.push({
                            lei: lei,
                            entityName: entity.legalName.name,
                            country: entity.legalAddress.country,
                            status: 'Found',
                            registrationDate: registration.initialRegistrationDate || 'N/A'
                        });
                    } else if (response.status === 404) {
                        resultsData.push({
                            lei: lei,
                            entityName: 'LEI not found in GLEIF database',
                            country: 'N/A',
                            status: 'Not Found',
                            registrationDate: 'N/A'
                        });
                    } else {
                        resultsData.push({
                            lei: lei,
                            entityName: `HTTP Error ${response.status}`,
                            country: 'N/A',
                            status: 'Error',
                            registrationDate: 'N/A'
                        });
                    }
                } catch (error) {
                    resultsData.push({
                        lei: lei,
                        entityName: 'Network/API Error',
                        country: 'N/A',
                        status: 'Error',
                        registrationDate: 'N/A'
                    });
                }
                completed++;
                
                // Rate limiting - small delay to prevent overwhelming the API
                await new Promise(resolve => setTimeout(resolve, 150));
            }

            // Hide progress and show results
            progressContainer.style.display = 'none';
            displayResults();
            
            // Update button state
            updateButtonLoading(false);
            if (resultsData.length > 0) {
                document.getElementById('exportCsvBtn').disabled = false;
                document.getElementById('exportExcelBtn').disabled = false;
            }
        }

        function displayResults() {
            const resultsSection = document.getElementById('resultsSection');
            const resultsBody = document.getElementById('resultsBody');
            
            // Update stats
            const totalCount = resultsData.length;
            const successCount = resultsData.filter(r => r.status === 'Found').length;
            const errorCount = totalCount - successCount;
            
            document.getElementById('totalCount').textContent = totalCount;
            document.getElementById('successCount').textContent = successCount;
            document.getElementById('errorCount').textContent = errorCount;
            
            // Clear and populate table
            resultsBody.innerHTML = '';
            
            resultsData.forEach(result => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td><span class="lei-code">${result.lei}</span></td>
                    <td>${result.entityName}</td>
                    <td>${result.country}</td>
                    <td class="${result.status === 'Found' ? 'status-success' : 'status-error'}">${result.status}</td>
                    <td>${result.registrationDate}</td>
                `;
                resultsBody.appendChild(row);
            });
            
            resultsSection.style.display = 'block';
        }

        function exportToCSV() {
            if (resultsData.length === 0) return;

            const headers = ['LEI Code', 'Entity Name', 'Country', 'Status', 'Registration Date'];
            const csvContent = [
                headers.join(','),
                ...resultsData.map(row => [
                    row.lei,
                    `"${row.entityName.replace(/"/g, '""')}"`,
                    row.country,
                    row.status,
                    row.registrationDate
                ].join(','))
            ].join('\n');

            const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
            const link = document.createElement('a');
            const url = URL.createObjectURL(blob);
            const timestamp = new Date().toISOString().split('T')[0];
            link.setAttribute('href', url);
            link.setAttribute('download', `lei_lookup_results_${timestamp}.csv`);
            link.style.visibility = 'hidden';
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
        }

        function exportToExcel() {
            if (resultsData.length === 0) return;

            const ws = XLSX.utils.json_to_sheet(resultsData.map(row => ({
                'LEI Code': row.lei,
                'Entity Name': row.entityName,
                'Country': row.country,
                'Status': row.status,
                'Registration Date': row.registrationDate
            })));

            // Auto-size columns
            const cols = [
                { wch: 22 }, // LEI Code
                { wch: 40 }, // Entity Name
                { wch: 12 }, // Country
                { wch: 12 }, // Status
                { wch: 18 }  // Registration Date
            ];
            ws['!cols'] = cols;

            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, ws, "LEI Lookup Results");

            const timestamp = new Date().toISOString().split('T')[0];
            XLSX.writeFile(wb, `lei_lookup_results_${timestamp}.xlsx`);
        }

        function clearResults() {
            if (confirm('🗑️ Are you sure you want to clear all data and results?')) {
                document.getElementById('leiInput').value = '';
                resultsData = [];
                document.getElementById('resultsSection').style.display = 'none';
                document.getElementById('progressContainer').style.display = 'none';
                document.getElementById('exportCsvBtn').disabled = true;
                document.getElementById('exportExcelBtn').disabled = true;
            }
        }

        // Keyboard shortcuts
        document.getElementById('leiInput').addEventListener('keydown', function(event) {
            if (event.ctrlKey && event.key === 'Enter') {
                event.preventDefault();
                lookupLEIs();
            }
        });

        // Initialize with focus on textarea
        window.addEventListener('load', function() {
            document.getElementById('leiInput').focus();
        });
    </script>
</body>
</html>
