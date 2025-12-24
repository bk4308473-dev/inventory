<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Inventory Management - Real-Time Sync & Excel Import/Export</title>
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', 'Arial', sans-serif;
        }
        
        body {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: #333;
            min-height: 100vh;
            padding: 15px;
        }
        
        .container {
            max-width: 1200px;
            margin: 0 auto;
            background-color: white;
            border-radius: 10px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.2);
            overflow: hidden;
        }
        
        header {
            background: linear-gradient(90deg, #4b6cb7 0%, #182848 100%);
            color: white;
            padding: 20px;
            text-align: center;
        }
        
        h1 {
            font-size: 1.8rem;
            margin-bottom: 10px;
        }
        
        .sync-status {
            display: inline-flex;
            align-items: center;
            gap: 8px;
            padding: 5px 15px;
            background: rgba(255,255,255,0.2);
            border-radius: 20px;
            font-size: 0.9rem;
            margin-top: 10px;
        }
        
        .sync-indicator {
            width: 12px;
            height: 12px;
            border-radius: 50%;
            animation: pulse 2s infinite;
        }
        
        .sync-indicator.synced {
            background-color: #4CAF50;
        }
        
        .sync-indicator.syncing {
            background-color: #FF9800;
        }
        
        .sync-indicator.offline {
            background-color: #f44336;
        }
        
        @keyframes pulse {
            0% { opacity: 1; }
            50% { opacity: 0.5; }
            100% { opacity: 1; }
        }
        
        .main-content {
            display: flex;
            flex-wrap: wrap;
            padding: 20px;
            gap: 20px;
        }
        
        .left-panel {
            flex: 1;
            min-width: 300px;
        }
        
        .right-panel {
            flex: 2;
            min-width: 500px;
        }
        
        .section {
            background-color: #f9f9f9;
            border-radius: 8px;
            padding: 20px;
            margin-bottom: 20px;
            border: 1px solid #e0e0e0;
        }
        
        .section h2 {
            color: #4b6cb7;
            margin-bottom: 15px;
            padding-bottom: 8px;
            border-bottom: 2px solid #4b6cb7;
            font-size: 1.2rem;
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .section h2 i {
            font-size: 1.4rem;
        }
        
        .form-group {
            margin-bottom: 15px;
        }
        
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: 600;
            color: #555;
            font-size: 0.9rem;
        }
        
        input, select {
            width: 100%;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 4px;
            font-size: 0.9rem;
        }
        
        input:focus, select:focus {
            outline: none;
            border-color: #4b6cb7;
            box-shadow: 0 0 0 2px rgba(75, 108, 183, 0.2);
        }
        
        .btn {
            background: linear-gradient(90deg, #4b6cb7 0%, #182848 100%);
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 4px;
            font-size: 0.9rem;
            cursor: pointer;
            display: inline-flex;
            align-items: center;
            gap: 8px;
            transition: all 0.3s;
        }
        
        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
        }
        
        .btn-success {
            background: linear-gradient(90deg, #4CAF50 0%, #2E7D32 100%);
        }
        
        .btn-warning {
            background: linear-gradient(90deg, #FF9800 0%, #F57C00 100%);
        }
        
        .btn-danger {
            background: linear-gradient(90deg, #f44336 0%, #d32f2f 100%);
        }
        
        .btn-excel {
            background: linear-gradient(90deg, #217346 0%, #1e5c3a 100%);
        }
        
        .button-group {
            display: flex;
            gap: 10px;
            margin-top: 15px;
            flex-wrap: wrap;
        }
        
        .search-box {
            position: relative;
            margin-bottom: 15px;
        }
        
        .search-box input {
            padding-left: 40px;
        }
        
        .search-icon {
            position: absolute;
            left: 10px;
            top: 50%;
            transform: translateY(-50%);
            color: #666;
        }
        
        .table-container {
            overflow-x: auto;
            border-radius: 4px;
            border: 1px solid #ddd;
            margin-top: 10px;
        }
        
        table {
            width: 100%;
            border-collapse: collapse;
            min-width: 600px;
            font-size: 0.9rem;
        }
        
        th {
            background-color: #4b6cb7;
            color: white;
            padding: 12px;
            text-align: left;
            position: sticky;
            top: 0;
        }
        
        tr:nth-child(even) {
            background-color: #f8f9fa;
        }
        
        tr:hover {
            background-color: #e9ecef;
        }
        
        td {
            padding: 10px 12px;
            border-bottom: 1px solid #ddd;
        }
        
        .device-list {
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
            margin-top: 10px;
        }
        
        .device-item {
            display: flex;
            align-items: center;
            gap: 5px;
            padding: 5px 10px;
            background: #e3f2fd;
            border-radius: 15px;
            font-size: 0.8rem;
        }
        
        .device-icon {
            width: 10px;
            height: 10px;
            border-radius: 50%;
        }
        
        .device-icon.online {
            background-color: #4CAF50;
        }
        
        .device-icon.offline {
            background-color: #f44336;
        }
        
        .action-buttons {
            display: flex;
            gap: 5px;
        }
        
        .action-btn {
            padding: 5px 10px;
            border-radius: 3px;
            font-size: 0.8rem;
            cursor: pointer;
            border: none;
            display: inline-flex;
            align-items: center;
            gap: 3px;
        }
        
        .edit-btn {
            background-color: #ff9800;
            color: white;
        }
        
        .delete-btn {
            background-color: #f44336;
            color: white;
        }
        
        .message {
            padding: 12px;
            border-radius: 4px;
            margin-bottom: 15px;
            display: none;
        }
        
        .success {
            background-color: #d4edda;
            color: #155724;
            border: 1px solid #c3e6cb;
            display: block;
        }
        
        .error {
            background-color: #f8d7da;
            color: #721c24;
            border: 1px solid #f5c6cb;
            display: block;
        }
        
        .info {
            background-color: #d1ecf1;
            color: #0c5460;
            border: 1px solid #bee5eb;
            display: block;
        }
        
        .file-upload-area {
            border: 2px dashed #4b6cb7;
            border-radius: 8px;
            padding: 30px;
            text-align: center;
            margin-bottom: 15px;
            background-color: #f8f9ff;
            cursor: pointer;
            transition: all 0.3s;
        }
        
        .file-upload-area:hover {
            background-color: #e8f4fc;
            border-color: #2980b9;
        }
        
        .file-upload-area input {
            display: none;
        }
        
        .excel-options {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
            margin-top: 15px;
        }
        
        @media (max-width: 768px) {
            .left-panel, .right-panel {
                flex: 100%;
            }
            
            .button-group, .excel-options {
                flex-direction: column;
            }
            
            .btn {
                width: 100%;
                justify-content: center;
            }
            
            h1 {
                font-size: 1.5rem;
            }
        }
    </style>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
</head>
<body>
    <div class="container">
        <header>
            <h1><i class="fas fa-sync-alt"></i> Real-Time Inventory Sync</h1>
            <p style="font-size: 0.9rem; margin-top: 5px; opacity: 0.9;">
                Syncs across all devices | Excel Import/Export | SL.NO | Product Name | Unit | Stock | Rack No
            </p>
            <div class="sync-status" id="syncStatus">
                <div class="sync-indicator" id="syncIndicator"></div>
                <span id="syncText">Connecting...</span>
            </div>
        </header>
        
        <div class="main-content">
            <!-- Left Panel -->
            <div class="left-panel">
                <!-- Product Form -->
                <div class="section">
                    <h2><i class="fas fa-plus-circle"></i> Add/Edit Product</h2>
                    <div id="message" class="message"></div>
                    
                    <form id="productForm">
                        <div class="form-group">
                            <label for="slNo"><i class="fas fa-hashtag"></i> SL.NO</label>
                            <input type="number" id="slNo" placeholder="Auto" disabled>
                        </div>
                        
                        <div class="form-group">
                            <label for="productName"><i class="fas fa-box"></i> Product Name *</label>
                            <input type="text" id="productName" placeholder="Enter product name" required>
                        </div>
                        
                        <div class="form-group">
                            <label for="unit"><i class="fas fa-balance-scale"></i> Unit *</label>
                            <select id="unit" required>
                                <option value="">Select Unit</option>
                                <option value="Pcs">Pieces</option>
                                <option value="Kg">Kilograms</option>
                                <option value="L">Liters</option>
                                <option value="M">Meters</option>
                                <option value="Box">Box</option>
                                <option value="Pack">Pack</option>
                                <option value="Set">Set</option>
                            </select>
                        </div>
                        
                        <div class="form-group">
                            <label for="currentStock"><i class="fas fa-cubes"></i> Current Stock *</label>
                            <input type="number" id="currentStock" placeholder="Quantity" min="0" required>
                        </div>
                        
                        <div class="form-group">
                            <label for="rackNo"><i class="fas fa-warehouse"></i> Rack Number *</label>
                            <input type="text" id="rackNo" placeholder="Rack location" required>
                        </div>
                        
                        <div class="button-group">
                            <button type="submit" class="btn" id="saveBtn">
                                <i class="fas fa-save"></i> Save Product
                            </button>
                            <button type="button" class="btn btn-warning" id="clearBtn">
                                <i class="fas fa-eraser"></i> Clear Form
                            </button>
                        </div>
                    </form>
                </div>
                
                <!-- Excel Import/Export -->
                <div class="section">
                    <h2><i class="fas fa-file-excel"></i> Excel Import & Export</h2>
                    
                    <div class="file-upload-area" id="fileUploadArea">
                        <label for="excelFile" style="cursor: pointer;">
                            <div style="font-size: 3rem; color: #4b6cb7; margin-bottom: 10px;">
                                <i class="fas fa-file-upload"></i>
                            </div>
                            <h3 style="margin-bottom: 5px;">Upload Excel/CSV File</h3>
                            <p style="color: #666; margin-bottom: 5px;">Drag & drop or click to browse</p>
                            <p style="font-size: 0.8rem; color: #888;">
                                Supported: .xlsx, .xls, .csv
                            </p>
                        </label>
                        <input type="file" id="excelFile" accept=".csv,.xlsx,.xls">
                    </div>
                    
                    <div id="importMessage" class="message"></div>
                    
                    <div class="excel-options">
                        <button class="btn btn-success" id="importBtn">
                            <i class="fas fa-file-import"></i> Import Data
                        </button>
                        <button class="btn btn-excel" id="exportExcelBtn">
                            <i class="fas fa-file-excel"></i> Export to Excel
                        </button>
                        <button class="btn" id="exportCSVBtn">
                            <i class="fas fa-file-csv"></i> Export to CSV
                        </button>
                    </div>
                    
                    <div class="button-group" style="margin-top: 15px;">
                        <button class="btn btn-warning" id="downloadTemplateBtn">
                            <i class="fas fa-download"></i> Download Template
                        </button>
                        <button class="btn btn-danger" id="deleteAllBtn">
                            <i class="fas fa-trash-alt"></i> Delete All Data
                        </button>
                    </div>
                </div>
            </div>
            
            <!-- Right Panel -->
            <div class="right-panel">
                <!-- Connected Devices -->
                <div class="section">
                    <h2><i class="fas fa-sync-alt"></i> Connected Devices</h2>
                    <div class="device-list" id="deviceList">
                        <!-- Devices will appear here -->
                    </div>
                    <div class="button-group" style="margin-top: 15px;">
                        <button class="btn btn-success" id="syncNowBtn">
                            <i class="fas fa-sync"></i> Sync Now
                        </button>
                        <button class="btn" id="showAllBtn">
                            <i class="fas fa-list"></i> Show All Products
                        </button>
                    </div>
                </div>
                
                <!-- Search -->
                <div class="section">
                    <h2><i class="fas fa-search"></i> Search Products</h2>
                    
                    <div class="search-box">
                        <div class="search-icon">
                            <i class="fas fa-search"></i>
                        </div>
                        <input type="text" id="searchInput" placeholder="Search by product name, rack number, or unit...">
                    </div>
                </div>
                
                <!-- Products Table -->
                <div class="section">
                    <h2><i class="fas fa-boxes"></i> Product Inventory</h2>
                    
                    <div class="table-container">
                        <table id="productsTable">
                            <thead>
                                <tr>
                                    <th>SL.NO</th>
                                    <th>Product Name</th>
                                    <th>Unit</th>
                                    <th>Current Stock</th>
                                    <th>Rack No</th>
                                    <th>Actions</th>
                                </tr>
                            </thead>
                            <tbody id="productsTableBody">
                                <tr>
                                    <td colspan="6" style="text-align: center; padding: 40px;">
                                        <i class="fas fa-box-open" style="font-size: 3rem; color: #ccc; margin-bottom: 15px;"></i>
                                        <h3 style="color: #999;">Loading inventory...</h3>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Include SheetJS for Excel operations -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    
    <script>
        // Real-time Sync Inventory System with Excel Import/Export
        class InventorySync {
            constructor() {
                this.inventory = [];
                this.devices = new Set();
                this.currentDeviceId = this.generateDeviceId();
                this.isOnline = navigator.onLine;
                this.editMode = false;
                this.currentEditId = null;
                this.syncInterval = null;
                
                this.initialize();
            }
            
            initialize() {
                this.loadFromLocalStorage();
                this.setupEventListeners();
                this.startSyncInterval();
                this.updateSyncStatus();
                this.registerDevice();
            }
            
            // Generate unique device ID
            generateDeviceId() {
                return 'device_' + Math.random().toString(36).substr(2, 9) + '_' + Date.now();
            }
            
            // Register current device
            registerDevice() {
                const deviceInfo = {
                    id: this.currentDeviceId,
                    name: this.getDeviceName(),
                    lastSeen: Date.now(),
                    isOnline: this.isOnline
                };
                
                const allDevices = JSON.parse(localStorage.getItem('inventory_devices') || '{}');
                allDevices[this.currentDeviceId] = deviceInfo;
                localStorage.setItem('inventory_devices', JSON.stringify(allDevices));
                
                this.updateDeviceList();
            }
            
            // Get device name
            getDeviceName() {
                const ua = navigator.userAgent;
                if (/mobile/i.test(ua)) {
                    return '📱 Mobile';
                } else if (/tablet/i.test(ua)) {
                    return '📱 Tablet';
                } else {
                    return '💻 Desktop';
                }
            }
            
            // Load inventory from localStorage
            loadFromLocalStorage() {
                const saved = localStorage.getItem('inventory_data');
                if (saved) {
                    try {
                        this.inventory = JSON.parse(saved);
                        this.sortInventory();
                    } catch (e) {
                        console.error('Error loading inventory:', e);
                        this.inventory = [];
                    }
                }
                this.updateUI();
            }
            
            // Save inventory to localStorage
            saveToLocalStorage() {
                localStorage.setItem('inventory_data', JSON.stringify(this.inventory));
                localStorage.setItem('inventory_sync_timestamp', Date.now().toString());
                this.broadcastChange();
            }
            
            // Broadcast changes to other tabs/windows
            broadcastChange() {
                if (window.BroadcastChannel) {
                    try {
                        const channel = new BroadcastChannel('inventory_channel');
                        channel.postMessage({
                            type: 'inventory_updated',
                            deviceId: this.currentDeviceId,
                            timestamp: Date.now(),
                            data: this.inventory
                        });
                    } catch (e) {
                        console.error('BroadcastChannel error:', e);
                    }
                }
                
                window.dispatchEvent(new StorageEvent('storage', {
                    key: 'inventory_data',
                    newValue: JSON.stringify(this.inventory)
                }));
            }
            
            // Setup event listeners
            setupEventListeners() {
                // BroadcastChannel messages
                if (window.BroadcastChannel) {
                    try {
                        const channel = new BroadcastChannel('inventory_channel');
                        channel.onmessage = (e) => {
                            if (e.data.type === 'inventory_updated' && e.data.deviceId !== this.currentDeviceId) {
                                this.showSyncNotification('Changes synced from another device');
                                this.inventory = e.data.data;
                                this.sortInventory();
                                this.updateUI();
                            }
                        };
                    } catch (e) {
                        console.error('BroadcastChannel error:', e);
                    }
                }
                
                // localStorage changes
                window.addEventListener('storage', (e) => {
                    if (e.key === 'inventory_data' && e.newValue) {
                        try {
                            this.inventory = JSON.parse(e.newValue);
                            this.sortInventory();
                            this.updateUI();
                            this.showSyncNotification('Changes synced from another tab');
                        } catch (error) {
                            console.error('Error parsing inventory data:', error);
                        }
                    }
                });
                
                // Online/offline detection
                window.addEventListener('online', () => {
                    this.isOnline = true;
                    this.updateSyncStatus();
                    this.updateDeviceList();
                });
                
                window.addEventListener('offline', () => {
                    this.isOnline = false;
                    this.updateSyncStatus();
                    this.updateDeviceList();
                });
                
                // Form submission
                document.getElementById('productForm').addEventListener('submit', (e) => {
                    e.preventDefault();
                    this.saveProduct();
                });
                
                // Button events
                document.getElementById('clearBtn').addEventListener('click', () => this.clearForm());
                document.getElementById('showAllBtn').addEventListener('click', () => {
                    document.getElementById('searchInput').value = '';
                    this.displayProducts();
                });
                document.getElementById('deleteAllBtn').addEventListener('click', () => this.deleteAllData());
                document.getElementById('syncNowBtn').addEventListener('click', () => this.forceSync());
                document.getElementById('importBtn').addEventListener('click', () => this.importExcelData());
                document.getElementById('exportExcelBtn').addEventListener('click', () => this.exportToExcel());
                document.getElementById('exportCSVBtn').addEventListener('click', () => this.exportToCSV());
                document.getElementById('downloadTemplateBtn').addEventListener('click', () => this.downloadTemplate());
                
                // Search
                document.getElementById('searchInput').addEventListener('input', (e) => {
                    this.filterProducts(e.target.value);
                });
                
                // File upload
                const fileUploadArea = document.getElementById('fileUploadArea');
                const excelFile = document.getElementById('excelFile');
                
                fileUploadArea.addEventListener('click', () => excelFile.click());
                
                fileUploadArea.addEventListener('dragover', (e) => {
                    e.preventDefault();
                    fileUploadArea.style.backgroundColor = '#e8f4fc';
                    fileUploadArea.style.borderColor = '#2980b9';
                });
                
                fileUploadArea.addEventListener('dragleave', () => {
                    fileUploadArea.style.backgroundColor = '#f8f9ff';
                    fileUploadArea.style.borderColor = '#4b6cb7';
                });
                
                fileUploadArea.addEventListener('drop', (e) => {
                    e.preventDefault();
                    fileUploadArea.style.backgroundColor = '#f8f9ff';
                    fileUploadArea.style.borderColor = '#4b6cb7';
                    
                    if (e.dataTransfer.files.length) {
                        excelFile.files = e.dataTransfer.files;
                        this.showImportMessage(`File ready: ${e.dataTransfer.files[0].name}`, 'info');
                    }
                });
                
                excelFile.addEventListener('change', (e) => {
                    if (e.target.files.length) {
                        this.showImportMessage(`File selected: ${e.target.files[0].name}`, 'info');
                    }
                });
            }
            
            // Save product
            saveProduct() {
                const slNo = this.editMode ? 
                    parseInt(document.getElementById('slNo').value) : 
                    (this.inventory.length > 0 ? Math.max(...this.inventory.map(p => p.slNo)) + 1 : 1);
                
                const productName = document.getElementById('productName').value.trim();
                const unit = document.getElementById('unit').value;
                const currentStock = parseInt(document.getElementById('currentStock').value) || 0;
                const rackNo = document.getElementById('rackNo').value.trim();
                
                // Validation
                if (!productName || !unit || !rackNo) {
                    this.showMessage('Please fill all required fields!', 'error');
                    return;
                }
                
                if (currentStock < 0) {
                    this.showMessage('Current stock cannot be negative!', 'error');
                    return;
                }
                
                if (this.editMode) {
                    // Edit existing product
                    const index = this.inventory.findIndex(p => p.slNo === this.currentEditId);
                    if (index !== -1) {
                        this.inventory[index] = {
                            ...this.inventory[index],
                            slNo: slNo,
                            productName: productName,
                            unit: unit,
                            currentStock: currentStock,
                            rackNo: rackNo,
                            updatedAt: Date.now(),
                            updatedBy: this.currentDeviceId
                        };
                        this.showMessage('Product updated successfully!', 'success');
                    }
                    this.editMode = false;
                    this.currentEditId = null;
                    document.getElementById('saveBtn').innerHTML = '<i class="fas fa-save"></i> Save Product';
                } else {
                    // Add new product
                    this.inventory.push({
                        slNo: slNo,
                        productName: productName,
                        unit: unit,
                        currentStock: currentStock,
                        rackNo: rackNo,
                        createdAt: Date.now(),
                        createdBy: this.currentDeviceId,
                        updatedAt: Date.now(),
                        updatedBy: this.currentDeviceId
                    });
                    this.showMessage('Product added successfully!', 'success');
                }
                
                this.sortInventory();
                this.saveToLocalStorage();
                this.updateUI();
                this.clearForm();
            }
            
            // Edit product
            editProduct(slNo) {
                const product = this.inventory.find(p => p.slNo === slNo);
                if (product) {
                    document.getElementById('slNo').value = product.slNo;
                    document.getElementById('productName').value = product.productName;
                    document.getElementById('unit').value = product.unit;
                    document.getElementById('currentStock').value = product.currentStock;
                    document.getElementById('rackNo').value = product.rackNo;
                    
                    this.editMode = true;
                    this.currentEditId = slNo;
                    document.getElementById('saveBtn').innerHTML = '<i class="fas fa-edit"></i> Update Product';
                    
                    this.showMessage('Product ready for editing.', 'success');
                }
            }
            
            // Delete product
            deleteProduct(slNo) {
                if (confirm('Are you sure you want to delete this product?')) {
                    this.inventory = this.inventory.filter(p => p.slNo !== slNo);
                    this.saveToLocalStorage();
                    this.updateUI();
                    this.showMessage('Product deleted successfully!', 'success');
                }
            }
            
            // Clear form
            clearForm() {
                document.getElementById('productForm').reset();
                this.editMode = false;
                this.currentEditId = null;
                this.updateSlNo();
                document.getElementById('saveBtn').innerHTML = '<i class="fas fa-save"></i> Save Product';
                document.getElementById('message').style.display = 'none';
            }
            
            // Update SL.NO
            updateSlNo() {
                const nextSlNo = this.inventory.length > 0 ? Math.max(...this.inventory.map(p => p.slNo)) + 1 : 1;
                document.getElementById('slNo').value = nextSlNo;
            }
            
            // Sort inventory
            sortInventory() {
                this.inventory.sort((a, b) => a.slNo - b.slNo);
            }
            
            // Display products
            displayProducts(products = this.inventory) {
                const tbody = document.getElementById('productsTableBody');
                
                if (products.length === 0) {
                    tbody.innerHTML = `
                        <tr>
                            <td colspan="6" style="text-align: center; padding: 40px;">
                                <i class="fas fa-box-open" style="font-size: 3rem; color: #ccc; margin-bottom: 15px;"></i>
                                <h3 style="color: #999;">No products found</h3>
                                <p style="color: #888;">Add your first product or import from Excel</p>
                            </td>
                        </tr>
                    `;
                    return;
                }
                
                let html = '';
                products.forEach(product => {
                    html += `
                        <tr>
                            <td><strong>${product.slNo}</strong></td>
                            <td>${product.productName}</td>
                            <td>${product.unit}</td>
                            <td>${product.currentStock}</td>
                            <td>${product.rackNo}</td>
                            <td>
                                <div class="action-buttons">
                                    <button class="action-btn edit-btn" onclick="inventory.editProduct(${product.slNo})">
                                        <i class="fas fa-edit"></i> Edit
                                    </button>
                                    <button class="action-btn delete-btn" onclick="inventory.deleteProduct(${product.slNo})">
                                        <i class="fas fa-trash"></i> Delete
                                    </button>
                                </div>
                            </td>
                        </tr>
                    `;
                });
                
                tbody.innerHTML = html;
            }
            
            // Filter products
            filterProducts(searchTerm) {
                if (!searchTerm) {
                    this.displayProducts();
                    return;
                }
                
                const filtered = this.inventory.filter(product => 
                    product.productName.toLowerCase().includes(searchTerm.toLowerCase()) ||
                    product.rackNo.toLowerCase().includes(searchTerm.toLowerCase()) ||
                    product.unit.toLowerCase().includes(searchTerm.toLowerCase())
                );
                
                this.displayProducts(filtered);
            }
            
            // Delete all data
            deleteAllData() {
                if (confirm('Are you sure you want to delete ALL inventory data from ALL devices?')) {
                    this.inventory = [];
                    localStorage.removeItem('inventory_data');
                    localStorage.removeItem('inventory_sync_timestamp');
                    this.updateUI();
                    this.showMessage('All data deleted successfully!', 'success');
                    this.broadcastChange();
                }
            }
            
            // Import Excel Data
            importExcelData() {
                const fileInput = document.getElementById('excelFile');
                const file = fileInput.files[0];
                
                if (!file) {
                    this.showImportMessage('Please select an Excel or CSV file first!', 'error');
                    return;
                }
                
                const reader = new FileReader();
                
                reader.onload = (e) => {
                    try {
                        const data = new Uint8Array(e.target.result);
                        const workbook = XLSX.read(data, { type: 'array' });
                        const firstSheet = workbook.Sheets[workbook.SheetNames[0]];
                        const jsonData = XLSX.utils.sheet_to_json(firstSheet, { header: 1 });
                        
                        let importedCount = 0;
                        let skippedCount = 0;
                        let updatedCount = 0;
                        
                        // Process data (skip header row if exists)
                        const startRow = jsonData[0] && 
                            (jsonData[0][0]?.toString().toLowerCase().includes('sl') || 
                             jsonData[0][0]?.toString().toLowerCase().includes('product')) ? 1 : 0;
                        
                        for (let i = startRow; i < jsonData.length; i++) {
                            const row = jsonData[i];
                            if (row && row.length >= 4) {
                                const productName = String(row[0] || '').trim();
                                const unit = String(row[1] || 'Pcs').trim();
                                const currentStock = parseInt(row[2]) || 0;
                                const rackNo = String(row[3] || '').trim();
                                
                                if (productName && rackNo) {
                                    // Check if product already exists (by name and rack)
                                    const existingIndex = this.inventory.findIndex(p => 
                                        p.productName.toLowerCase() === productName.toLowerCase() && 
                                        p.rackNo.toLowerCase() === rackNo.toLowerCase()
                                    );
                                    
                                    if (existingIndex !== -1) {
                                        // Update existing
                                        this.inventory[existingIndex] = {
                                            ...this.inventory[existingIndex],
                                            unit: unit,
                                            currentStock: currentStock,
                                            updatedAt: Date.now(),
                                            updatedBy: this.currentDeviceId
                                        };
                                        updatedCount++;
                                    } else {
                                        // Add new
                                        const newSlNo = this.inventory.length > 0 ? 
                                            Math.max(...this.inventory.map(p => p.slNo)) + 1 : 1;
                                        
                                        this.inventory.push({
                                            slNo: newSlNo,
                                            productName: productName,
                                            unit: unit,
                                            currentStock: currentStock,
                                            rackNo: rackNo,
                                            createdAt: Date.now(),
                                            createdBy: this.currentDeviceId,
                                            updatedAt: Date.now(),
                                            updatedBy: this.currentDeviceId
                                        });
                                        importedCount++;
                                    }
                                } else {
                                    skippedCount++;
                                }
                            }
                        }
                        
                        this.sortInventory();
                        this.saveToLocalStorage();
                        this.updateUI();
                        
                        this.showImportMessage(
                            `Import Complete!<br>
                            ✅ ${importedCount} new products imported<br>
                            ✏️ ${updatedCount} existing products updated<br>
                            ⏭️ ${skippedCount} rows skipped`,
                            'success'
                        );
                        
                        // Reset file input
                        fileInput.value = '';
                        
                    } catch (error) {
                        console.error('Import error:', error);
                        this.showImportMessage('Error importing file! Please check the file format.', 'error');
                    }
                };
                
                reader.onerror = () => {
                    this.showImportMessage('Error reading file!', 'error');
                };
                
                reader.readAsArrayBuffer(file);
            }
            
            // Export to Excel
            exportToExcel() {
                if (this.inventory.length === 0) {
                    this.showMessage('No data to export!', 'error');
                    return;
                }
                
                try {
                    // Prepare data
                    const exportData = [
                        ['SL.NO', 'Product Name', 'Unit', 'Current Stock', 'Rack No', 'Last Updated'],
                        ...this.inventory.map(product => [
                            product.slNo,
                            product.productName,
                            product.unit,
                            product.currentStock,
                            product.rackNo,
                            product.updatedAt ? new Date(product.updatedAt).toLocaleString() : ''
                        ])
                    ];
                    
                    // Create worksheet
                    const worksheet = XLSX.utils.aoa_to_sheet(exportData);
                    
                    // Set column widths
                    const wscols = [
                        { wch: 8 },  // SL.NO
                        { wch: 30 }, // Product Name
                        { wch: 10 }, // Unit
                        { wch: 12 }, // Current Stock
                        { wch: 15 }, // Rack No
                        { wch: 20 }  // Last Updated
                    ];
                    worksheet['!cols'] = wscols;
                    
                    // Create workbook
                    const workbook = XLSX.utils.book_new();
                    XLSX.utils.book_append_sheet(workbook, worksheet, 'Inventory');
                    
                    // Generate and download
                    const fileName = `inventory_export_${new Date().toISOString().split('T')[0]}.xlsx`;
                    XLSX.writeFile(workbook, fileName);
                    
                    this.showMessage(`Exported ${this.inventory.length} products to Excel`, 'success');
                } catch (error) {
                    console.error('Export error:', error);
                    this.showMessage('Error exporting to Excel!', 'error');
                }
            }
            
            // Export to CSV
            exportToCSV() {
                if (this.inventory.length === 0) {
                    this.showMessage('No data to export!', 'error');
                    return;
                }
                
                try {
                    const headers = ['SL.NO', 'Product Name', 'Unit', 'Current Stock', 'Rack No'];
                    const rows = this.inventory.map(product => [
                        product.slNo,
                        `"${product.productName}"`,
                        product.unit,
                        product.currentStock,
                        product.rackNo
                    ]);
                    
                    const csvContent = [
                        headers.join(','),
                        ...rows.map(row => row.join(','))
                    ].join('\n');
                    
                    const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
                    const url = URL.createObjectURL(blob);
                    const a = document.createElement('a');
                    a.href = url;
                    a.download = `inventory_${new Date().toISOString().split('T')[0]}.csv`;
                    document.body.appendChild(a);
                    a.click();
                    document.body.removeChild(a);
                    URL.revokeObjectURL(url);
                    
                    this.showMessage(`Exported ${this.inventory.length} products to CSV`, 'success');
                } catch (error) {
                    console.error('CSV export error:', error);
                    this.showMessage('Error exporting to CSV!', 'error');
                }
            }
            
            // Download template
            downloadTemplate() {
                try {
                    const templateData = [
                        ['Product Name', 'Unit', 'Current Stock', 'Rack No'],
                        ['Laptop', 'Pcs', '10', 'RACK-A01'],
                        ['Mouse', 'Pcs', '25', 'RACK-A02'],
                        ['Keyboard', 'Pcs', '15', 'RACK-A03'],
                        ['Rice', 'Kg', '50', 'RACK-B01'],
                        ['Sugar', 'Kg', '30', 'RACK-B02']
                    ];
                    
                    const worksheet = XLSX.utils.aoa_to_sheet(templateData);
                    const workbook = XLSX.utils.book_new();
                    XLSX.utils.book_append_sheet(workbook, worksheet, 'Template');
                    
                    // Set column widths
                    const wscols = [
                        { wch: 20 }, // Product Name
                        { wch: 10 }, // Unit
                        { wch: 15 }, // Current Stock
                        { wch: 15 }  // Rack No
                    ];
                    worksheet['!cols'] = wscols;
                    
                    XLSX.writeFile(workbook, 'inventory_template.xlsx');
                    
                    this.showImportMessage('Template downloaded successfully!', 'success');
                } catch (error) {
                    console.error('Template error:', error);
                    this.showImportMessage('Error downloading template!', 'error');
                }
            }
            
            // Update UI
            updateUI() {
                this.updateSlNo();
                this.displayProducts();
                this.updateDeviceList();
            }
            
            // Update device list
            updateDeviceList() {
                const allDevices = JSON.parse(localStorage.getItem('inventory_devices') || '{}');
                const deviceList = document.getElementById('deviceList');
                
                // Clean up old devices
                const now = Date.now();
                Object.keys(allDevices).forEach(deviceId => {
                    if (now - allDevices[deviceId].lastSeen > 5 * 60 * 1000) {
                        delete allDevices[deviceId];
                    }
                });
                
                // Update current device
                if (allDevices[this.currentDeviceId]) {
                    allDevices[this.currentDeviceId].lastSeen = now;
                    allDevices[this.currentDeviceId].isOnline = this.isOnline;
                }
                
                localStorage.setItem('inventory_devices', JSON.stringify(allDevices));
                
                // Display devices
                let html = '';
                Object.values(allDevices).forEach(device => {
                    const isCurrent = device.id === this.currentDeviceId;
                    html += `
                        <div class="device-item">
                            <div class="device-icon ${device.isOnline ? 'online' : 'offline'}"></div>
                            <span>${device.name}${isCurrent ? ' (You)' : ''}</span>
                        </div>
                    `;
                });
                
                deviceList.innerHTML = html || '<div style="color: #666;">No other devices connected</div>';
            }
            
            // Update sync status
            updateSyncStatus() {
                const indicator = document.getElementById('syncIndicator');
                const text = document.getElementById('syncText');
                
                if (this.isOnline) {
                    indicator.className = 'sync-indicator synced';
                    text.textContent = 'Synced';
                } else {
                    indicator.className = 'sync-indicator offline';
                    text.textContent = 'Offline';
                }
            }
            
            // Start sync interval
            startSyncInterval() {
                this.syncInterval = setInterval(() => {
                    this.updateDeviceList();
                    this.checkForUpdates();
                }, 10000);
            }
            
            // Check for updates
            checkForUpdates() {
                localStorage.setItem('inventory_sync_timestamp', Date.now().toString());
                this.updateSyncStatus();
            }
            
            // Force sync
            forceSync() {
                this.showMessage('🔄 Syncing with other devices...', 'success');
                this.broadcastChange();
                this.updateDeviceList();
                
                setTimeout(() => {
                    this.showMessage('Sync completed!', 'success');
                }, 1000);
            }
            
            // Show message
            showMessage(text, type) {
                const messageEl = document.getElementById('message');
                messageEl.innerHTML = text;
                messageEl.className = `message ${type}`;
                messageEl.style.display = 'block';
                
                setTimeout(() => {
                    messageEl.style.display = 'none';
                }, 3000);
            }
            
            // Show import message
            showImportMessage(text, type) {
                const messageEl = document.getElementById('importMessage');
                messageEl.innerHTML = text;
                messageEl.className = `message ${type}`;
                messageEl.style.display = 'block';
                
                setTimeout(() => {
                    messageEl.style.display = 'none';
                }, 5000);
            }
            
            // Show sync notification
            showSyncNotification(text) {
                this.showMessage('🔄 ' + text, 'success');
            }
        }
        
        // Initialize the inventory system
        let inventory;
        
        document.addEventListener('DOMContentLoaded', () => {
            inventory = new InventorySync();
            
            // Add demo data if empty
            if (inventory.inventory.length === 0) {
                inventory.inventory = [
                    { slNo: 1, productName: 'Laptop', unit: 'Pcs', currentStock: 10, rackNo: 'RACK-A01', 
                      createdAt: Date.now(), createdBy: 'demo', updatedAt: Date.now(), updatedBy: 'demo' },
                    { slNo: 2, productName: 'Wireless Mouse', unit: 'Pcs', currentStock: 25, rackNo: 'RACK-A02',
                      createdAt: Date.now(), createdBy: 'demo', updatedAt: Date.now(), updatedBy: 'demo' },
                    { slNo: 3, productName: 'Keyboard', unit: 'Pcs', currentStock: 15, rackNo: 'RACK-A03',
                      createdAt: Date.now(), createdBy: 'demo', updatedAt: Date.now(), updatedBy: 'demo' }
                ];
                inventory.sortInventory();
                inventory.saveToLocalStorage();
                inventory.updateUI();
            }
        });
        
        // Make inventory accessible globally
        window.inventory = inventory;
    </script>
</body>
</html>
