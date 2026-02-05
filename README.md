<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bảng Tính Khối Lượng Gỗ</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome for Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Roboto:wght@300;400;500;700&display=swap');
        
        body {
            font-family: 'Roboto', sans-serif;
            background-color: #f3f4f6;
        }

        /* Tùy chỉnh thanh cuộn */
        ::-webkit-scrollbar {
            width: 8px;
            height: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #f1f1f1; 
        }
        ::-webkit-scrollbar-thumb {
            background: #888; 
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #555; 
        }

        /* Ẩn input mũi tên tăng giảm số */
        input::-webkit-outer-spin-button,
        input::-webkit-inner-spin-button {
            -webkit-appearance: none;
            margin: 0;
        }
        input[type=number] {
            -moz-appearance: textfield;
        }

        /* Cấu hình in ấn */
        @media print {
            @page {
                size: A4;
                margin: 1cm;
            }
            body {
                background-color: white;
                -webkit-print-color-adjust: exact;
            }
            .no-print {
                display: none !important;
            }
            .print-only {
                display: block !important;
            }
            .shadow-lg, .shadow-md, .rounded-lg, .rounded-xl {
                box-shadow: none !important;
                border-radius: 0 !important;
                border: none !important;
            }
            table {
                border-collapse: collapse;
                width: 100%;
                font-size: 12px;
            }
            th, td {
                border: 1px solid black !important;
                padding: 4px !important;
            }
            /* Đảm bảo tiêu đề bảng có nền xám khi in */
            thead th {
                background-color: #e5e7eb !important;
                color: black !important;
            }
            /* Ẩn cột hành động khi in */
            .action-col {
                display: none !important;
            }
            /* Màu nền cho dòng được chọn khi in (tùy chọn, thường để trắng) */
            .bg-blue-200 {
                background-color: white !important;
            }
            /* Màu tiêu đề nhóm khi in */
            .group-header {
                background-color: #f3f4f6 !important;
                font-weight: bold;
            }
            /* Màu nền cho dòng tổng phụ khi in */
            .subtotal-row {
                background-color: #f9fafb !important;
                font-weight: bold;
            }
            /* Ngắt trang thông minh cho bảng */
            tr {
                page-break-inside: avoid;
            }
        }
    </style>
</head>
<body class="text-gray-800 p-4 min-h-screen flex flex-col">

    <!-- Header & Controls -->
    <header class="max-w-5xl mx-auto w-full mb-6 no-print">
        <div class="flex flex-col md:flex-row justify-between items-center gap-4 bg-white p-4 rounded-xl shadow-md">
            <div>
                <h1 class="text-2xl font-bold text-blue-800 uppercase tracking-wide"><i class="fas fa-tree mr-2"></i>Tính Khối Lượng Gỗ</h1>
                <p class="text-sm text-gray-500">Công thức: Dày(mm) x Rộng(cm) x Dài(x10cm) / 1,000,000</p>
            </div>
            <div class="flex gap-2">
                <button onclick="undo()" id="btnUndo" class="px-4 py-2 bg-yellow-500 hover:bg-yellow-600 text-white rounded-lg transition disabled:opacity-50 disabled:cursor-not-allowed" title="Hoàn tác (Ctrl+Z)">
                    <i class="fas fa-undo mr-1"></i> Hoàn tác
                </button>
                <button onclick="confirmClear()" class="px-4 py-2 bg-red-500 hover:bg-red-600 text-white rounded-lg transition" title="Tạo hóa đơn mới">
                    <i class="fas fa-trash-alt mr-1"></i> Hủy Hóa Đơn
                </button>
                <button onclick="window.print()" class="px-4 py-2 bg-blue-600 hover:bg-blue-700 text-white rounded-lg transition shadow-lg" title="In Hóa Đơn">
                    <i class="fas fa-print mr-1"></i> In Hóa Đơn
                </button>
            </div>
        </div>
    </header>

    <!-- Input Area -->
    <section class="max-w-5xl mx-auto w-full mb-6 no-print sticky top-2 z-10">
        <div class="bg-white p-4 rounded-xl shadow-lg border border-blue-100">
            <form id="addForm" onsubmit="event.preventDefault(); addItem();" class="grid grid-cols-1 md:grid-cols-12 gap-3 items-end">
                
                <!-- Loại Gỗ & Nút Edit Loại -->
                <div class="md:col-span-3 relative">
                    <label class="block text-xs font-bold text-gray-600 mb-1">LOẠI GỖ</label>
                    <div class="flex">
                        <select id="inputType" class="w-full p-2 border border-gray-300 rounded-l-lg focus:outline-none focus:ring-2 focus:ring-blue-500 bg-gray-50">
                            <!-- Options will be loaded by JS -->
                        </select>
                        <button type="button" onclick="openTypeModal()" class="bg-gray-200 hover:bg-gray-300 px-2 rounded-r-lg border border-l-0 border-gray-300 text-gray-600" title="Chỉnh sửa danh sách loại gỗ">
                            <i class="fas fa-cog"></i>
                        </button>
                    </div>
                </div>

                <div class="md:col-span-2">
                    <label class="block text-xs font-bold text-gray-600 mb-1">DÀY (mm)</label>
                    <input type="number" step="0.1" id="inputThick" class="w-full p-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 text-center font-mono" required placeholder="Ví dụ: 20">
                </div>

                <div class="md:col-span-2">
                    <label class="block text-xs font-bold text-gray-600 mb-1">RỘNG (cm)</label>
                    <input type="number" step="0.1" id="inputWidth" class="w-full p-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 text-center font-mono" required placeholder="Ví dụ: 30">
                </div>

                <div class="md:col-span-2">
                    <label class="block text-xs font-bold text-gray-600 mb-1">DÀI (x10cm)</label>
                    <input type="number" step="0.1" id="inputLength" class="w-full p-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 text-center font-mono" required placeholder="Ví dụ: 25">
                </div>

                <div class="md:col-span-3">
                    <button type="submit" class="w-full py-2 bg-green-600 hover:bg-green-700 text-white font-bold rounded-lg shadow-md transition transform active:scale-95">
                        <i class="fas fa-plus mr-1"></i> THÊM TẤM
                    </button>
                </div>
            </form>
        </div>
    </section>

    <!-- Main Content for Print & View -->
    <main class="max-w-5xl mx-auto w-full bg-white rounded-xl shadow-md overflow-hidden flex-grow flex flex-col">
        
        <!-- Header Print Only -->
        <div class="hidden print-only p-4 border-b text-center">
            <h1 class="text-2xl font-bold uppercase">Bảng Kê Chi Tiết Gỗ Xẻ</h1>
            <p class="text-sm text-gray-600 mt-1">Ngày: <span id="printDate"></span></p>
        </div>

        <!-- Main Table -->
        <div class="overflow-x-auto">
            <table class="w-full text-left border-collapse">
                <thead class="bg-blue-50 text-blue-900 border-b border-blue-200">
                    <tr>
                        <th class="p-3 text-center w-12 border-r border-blue-100 font-bold">STT</th>
                        <!-- Removed Wood Type Column Header -->
                        <!-- Removed Thick Column Header -->
                        <th class="p-3 text-center border-r border-blue-100 font-bold">Rộng <span class="text-xs font-normal text-gray-500">(cm)</span></th>
                        <th class="p-3 text-center border-r border-blue-100 font-bold">Dài <span class="text-xs font-normal text-gray-500">(x10cm)</span></th>
                        <th class="p-3 text-right font-bold">Khối Lượng <span class="text-xs font-normal text-gray-500">(m³)</span></th>
                        <th class="p-3 text-center action-col w-24 no-print">Tác vụ</th>
                    </tr>
                </thead>
                <tbody id="woodTableBody">
                    <!-- Data rows generated here -->
                </tbody>
            </table>
        </div>

        <!-- No Data Message -->
        <div id="emptyState" class="p-8 text-center text-gray-400">
            <i class="fas fa-clipboard-list text-4xl mb-3"></i>
            <p>Chưa có dữ liệu gỗ. Vui lòng nhập thông tin ở trên.</p>
        </div>

        <!-- Summary Section (Separated by Type) - Hidden on Print -->
        <div class="p-6 border-t border-dashed border-gray-300 bg-gray-50 mt-auto no-print">
            <h3 class="text-lg font-bold text-gray-700 uppercase mb-3 border-l-4 border-blue-500 pl-3">Tổng Hợp Theo Loại Hàng</h3>
            <div id="summaryContainer" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
                <!-- Summary cards generated here -->
            </div>
        </div>
    </main>

    <!-- Modal Edit Type -->
    <div id="typeModal" class="fixed inset-0 bg-black bg-opacity-50 hidden items-center justify-center z-50 no-print">
        <div class="bg-white rounded-lg shadow-xl w-96 p-6">
            <h3 class="text-lg font-bold mb-4 text-gray-800">Quản Lý Loại Gỗ</h3>
            <div class="flex gap-2 mb-4">
                <input type="text" id="newTypeInput" class="flex-1 border p-2 rounded focus:outline-none focus:border-blue-500" placeholder="Nhập tên loại mới...">
                <button onclick="addNewType()" class="bg-blue-600 text-white px-3 py-2 rounded hover:bg-blue-700"><i class="fas fa-plus"></i></button>
            </div>
            <ul id="typeList" class="space-y-2 max-h-60 overflow-y-auto border-t pt-2">
                <!-- List items -->
            </ul>
            <div class="mt-6 flex justify-end">
                <button onclick="closeTypeModal()" class="px-4 py-2 bg-gray-200 hover:bg-gray-300 rounded text-gray-700 font-medium">Đóng</button>
            </div>
        </div>
    </div>

    <!-- Modal Edit Item -->
    <div id="editModal" class="fixed inset-0 bg-black bg-opacity-50 hidden items-center justify-center z-50 no-print">
        <div class="bg-white rounded-lg shadow-xl w-full max-w-md p-6">
            <h3 class="text-lg font-bold mb-4 text-blue-800">Chỉnh Sửa Tấm Gỗ</h3>
            <input type="hidden" id="editIndex">
            
            <div class="space-y-3">
                <div>
                    <label class="block text-sm font-medium text-gray-700">Loại Gỗ</label>
                    <select id="editType" class="w-full mt-1 p-2 border rounded-md"></select>
                </div>
                <div class="grid grid-cols-3 gap-3">
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Dày (mm)</label>
                        <input type="number" step="0.1" id="editThick" class="w-full mt-1 p-2 border rounded-md text-center">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Rộng (cm)</label>
                        <input type="number" step="0.1" id="editWidth" class="w-full mt-1 p-2 border rounded-md text-center">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Dài (x10)</label>
                        <input type="number" step="0.1" id="editLength" class="w-full mt-1 p-2 border rounded-md text-center">
                    </div>
                </div>
            </div>

            <div class="mt-6 flex justify-end gap-3">
                <button onclick="closeEditModal()" class="px-4 py-2 bg-gray-200 hover:bg-gray-300 rounded text-gray-700">Hủy</button>
                <button onclick="saveEdit()" class="px-4 py-2 bg-blue-600 hover:bg-blue-700 text-white rounded shadow">Lưu Thay Đổi</button>
            </div>
        </div>
    </div>

    <!-- Script Logic -->
    <script>
        // --- DATA & STATE ---
        let items = [];
        let historyStack = [];
        let woodTypes = ["Sồi đỏ", "Thông", "Ash", "Thông tuyến tính", "Dương vàng", "Xoan đào", "    "];
        let selectedId = null; // Track selected item

        // Formatters
        const volumeFormatter = new Intl.NumberFormat('en-US', { minimumFractionDigits: 4, maximumFractionDigits: 4 });

        // --- INIT ---
        window.onload = function() {
            renderTypeOptions();
            document.getElementById('printDate').innerText = new Date().toLocaleDateString('vi-VN');
            
            const inputThick = document.getElementById('inputThick');
            const inputWidth = document.getElementById('inputWidth');
            const inputLength = document.getElementById('inputLength');

            // Focus on Thickness input initially
            inputThick.focus();

            // --- AUTO FOCUS LOGIC ---
            
            // 1. Auto focus Length when Width has 2 chars
            inputWidth.addEventListener('input', function() {
                if (this.value.length >= 2) {
                    inputLength.focus();
                }
            });

            // 2. Auto focus Width when deleting from Length (Backspace on empty)
            inputLength.addEventListener('keydown', function(e) {
                if (e.key === 'Backspace' && this.value === '') {
                    inputWidth.focus();
                }
            });
        };

        // --- HISTORY / UNDO ---
        function saveToHistory() {
            // Keep max 20 steps
            if (historyStack.length > 20) historyStack.shift();
            historyStack.push(JSON.stringify(items));
            updateUndoButton();
        }

        function undo() {
            if (historyStack.length === 0) return;
            items = JSON.parse(historyStack.pop());
            selectedId = null; // Clear selection on undo
            renderTable();
            renderSummary();
            updateUndoButton();
        }

        function updateUndoButton() {
            const btn = document.getElementById('btnUndo');
            btn.disabled = historyStack.length === 0;
            btn.classList.toggle('opacity-50', historyStack.length === 0);
        }

        // --- CORE LOGIC ---
        function calculateVolume(thick, width, length) {
            // Formula: (mm * cm * 10cm) / 1,000,000 = m3
            const val = (thick * width * length) / 1000000;
            return parseFloat(val.toFixed(5)); // Keep precision internally
        }

        function addItem() {
            const type = document.getElementById('inputType').value;
            const thick = parseFloat(document.getElementById('inputThick').value);
            const width = parseFloat(document.getElementById('inputWidth').value);
            const length = parseFloat(document.getElementById('inputLength').value);

            if (!type || isNaN(thick) || isNaN(width) || isNaN(length)) return;

            saveToHistory();

            const newItem = {
                id: Date.now(),
                type,
                thick,
                width,
                length,
                volume: calculateVolume(thick, width, length)
            };

            // INSERT LOGIC: Add after selected item, or push to end if none selected
            if (selectedId) {
                const index = items.findIndex(item => item.id === selectedId);
                if (index !== -1) {
                    items.splice(index + 1, 0, newItem);
                } else {
                    items.push(newItem);
                }
            } else {
                items.push(newItem);
            }

            selectedId = newItem.id; // Auto select new item to continue chaining
            
            renderTable();
            renderSummary();

            document.getElementById('inputThick').value = thick; 
            document.getElementById('inputWidth').value = "";
            document.getElementById('inputLength').value = "";
            document.getElementById('inputWidth').focus();
        }

        function selectItem(id) {
            // Toggle selection logic: Click again to deselect
            if (selectedId === id) {
                selectedId = null;
            } else {
                selectedId = id;
            }
            renderTable(); // Re-render to show highlight
        }

        function deleteItem(index) {
            saveToHistory();
            const deletedId = items[index].id;
            items.splice(index, 1);
            if (selectedId === deletedId) selectedId = null;
            renderTable();
            renderSummary();
        }

        function confirmClear() {
            if (items.length > 0) {
                if (confirm("Bạn có chắc chắn muốn xóa toàn bộ hóa đơn và tạo mới?")) {
                    saveToHistory();
                    items = [];
                    selectedId = null;
                    renderTable();
                    renderSummary();
                }
            }
        }

        // --- EDITING ---
        function openEditModal(index) {
            const item = items[index];
            document.getElementById('editIndex').value = index;
            
            // Populate Types
            const select = document.getElementById('editType');
            select.innerHTML = '';
            woodTypes.forEach(t => {
                const opt = document.createElement('option');
                opt.value = t;
                opt.text = t;
                select.appendChild(opt);
            });
            select.value = item.type;

            document.getElementById('editThick').value = item.thick;
            document.getElementById('editWidth').value = item.width;
            document.getElementById('editLength').value = item.length;

            document.getElementById('editModal').classList.remove('hidden');
            document.getElementById('editModal').classList.add('flex');
            
            // Update selected ID to the one being edited
            selectedId = item.id;
            renderTable();
        }

        function closeEditModal() {
            document.getElementById('editModal').classList.add('hidden');
            document.getElementById('editModal').classList.remove('flex');
        }

        function saveEdit() {
            const index = parseInt(document.getElementById('editIndex').value);
            const type = document.getElementById('editType').value;
            const thick = parseFloat(document.getElementById('editThick').value);
            const width = parseFloat(document.getElementById('editWidth').value);
            const length = parseFloat(document.getElementById('editLength').value);

            if (isNaN(thick) || isNaN(width) || isNaN(length)) return;

            saveToHistory();

            items[index] = {
                ...items[index],
                type, thick, width, length,
                volume: calculateVolume(thick, width, length)
            };

            closeEditModal();
            renderTable();
            renderSummary();
        }

        // --- TYPE MANAGEMENT ---
        function openTypeModal() {
            renderTypeModalList();
            document.getElementById('typeModal').classList.remove('hidden');
            document.getElementById('typeModal').classList.add('flex');
        }

        function closeTypeModal() {
            document.getElementById('typeModal').classList.add('hidden');
            document.getElementById('typeModal').classList.remove('flex');
            renderTypeOptions(); // Update main dropdown
        }

        function renderTypeOptions() {
            const select = document.getElementById('inputType');
            const currentVal = select.value;
            select.innerHTML = '';
            woodTypes.forEach(t => {
                const opt = document.createElement('option');
                opt.value = t;
                opt.text = t;
                select.appendChild(opt);
            });
            if(woodTypes.includes(currentVal)) select.value = currentVal;
        }

        function renderTypeModalList() {
            const list = document.getElementById('typeList');
            list.innerHTML = '';
            woodTypes.forEach((t, i) => {
                const li = document.createElement('li');
                li.className = "flex justify-between items-center bg-gray-50 p-2 rounded";
                li.innerHTML = `
                    <span>${t}</span>
                    <button onclick="removeType(${i})" class="text-red-500 hover:text-red-700 ${woodTypes.length <= 1 ? 'hidden' : ''}">
                        <i class="fas fa-times"></i>
                    </button>
                `;
                list.appendChild(li);
            });
        }

        function addNewType() {
            const input = document.getElementById('newTypeInput');
            const val = input.value.trim();
            if (val && !woodTypes.includes(val)) {
                woodTypes.push(val);
                input.value = '';
                renderTypeModalList();
            }
        }

        function removeType(index) {
            if (woodTypes.length > 1) {
                woodTypes.splice(index, 1);
                renderTypeModalList();
            }
        }

        // --- RENDERING ---
        function renderTable() {
            const tbody = document.getElementById('woodTableBody');
            const emptyState = document.getElementById('emptyState');
            
            tbody.innerHTML = '';
            let totalVol = 0;
            let groupVol = 0;
            let groupSTT = 1;

            if (items.length === 0) {
                emptyState.style.display = 'block';
                return;
            }
            emptyState.style.display = 'none';

            items.forEach((item, index) => {
                // UPDATE: Grouping Logic (Type OR Thickness change)
                let isNewGroup = false;
                if (index === 0) {
                    isNewGroup = true;
                } else {
                    const prev = items[index-1];
                    // Change if Type differs OR Thickness differs
                    if (prev.type !== item.type || prev.thick !== item.thick) {
                        isNewGroup = true;
                    }
                }
                
                // UPDATE: Render Subtotal for previous group before starting new one
                if (isNewGroup && index > 0) {
                     const prev = items[index-1];
                     const subTr = document.createElement('tr');
                     // colSpan reduced by 2 (Both Type and Thickness columns are removed)
                     subTr.className = "subtotal-row bg-gray-100 font-bold text-gray-700 italic border-b border-gray-300";
                     subTr.innerHTML = `
                        <td colspan="3" class="p-2 text-right text-sm">Tổng ${prev.type} (${prev.thick}mm):</td>
                        <td class="p-2 text-right text-blue-800 text-sm">${volumeFormatter.format(groupVol)}</td>
                        <td class="action-col no-print"></td>
                    `;
                    tbody.appendChild(subTr);
                    
                    // Reset group counters
                    groupVol = 0;
                    groupSTT = 1;
                }

                // UPDATE: Render Group Header
                if (isNewGroup) {
                    const headerTr = document.createElement('tr');
                    headerTr.className = "group-header bg-blue-100 text-blue-900";
                    // Display Type and Thickness in header. colSpan reduced by 2.
                    headerTr.innerHTML = `
                        <td colspan="5" class="p-2 pl-4 font-bold uppercase border-t-2 border-blue-200">
                            <i class="fas fa-layer-group mr-2"></i> ${item.type} <span class="text-sm font-normal text-gray-600 normal-case">- Dày: ${item.thick}mm</span>
                        </td>
                    `;
                    tbody.appendChild(headerTr);
                }

                // Update Volumes
                totalVol += item.volume;
                groupVol += item.volume;

                // Check selection
                const isSelected = item.id === selectedId;
                const rowClass = isSelected ? 'bg-blue-200 cursor-pointer' : 'hover:bg-blue-50 cursor-pointer transition duration-150';

                const tr = document.createElement('tr');
                tr.className = `${rowClass} border-b border-gray-100`;
                tr.onclick = (e) => {
                    if(e.target.closest('button')) return;
                    selectItem(item.id);
                };

                // Cells - Type and Thick columns removed
                tr.innerHTML = `
                    <td class="p-3 text-center text-gray-500 text-sm">${groupSTT}</td>
                    <td class="p-3 text-center">${item.width}</td>
                    <td class="p-3 text-center">${item.length}</td>
                    <td class="p-3 text-right font-mono font-medium text-blue-700">${volumeFormatter.format(item.volume)}</td>
                    <td class="p-3 text-center action-col">
                        <button onclick="openEditModal(${index})" class="text-blue-500 hover:text-blue-700 mx-1 p-1" title="Sửa"><i class="fas fa-edit"></i></button>
                        <button onclick="deleteItem(${index})" class="text-red-400 hover:text-red-600 mx-1 p-1" title="Xóa"><i class="fas fa-trash"></i></button>
                    </td>
                `;
                tbody.appendChild(tr);

                groupSTT++;

                // Handle subtotal for the very last item
                if (index === items.length - 1) {
                     const subTr = document.createElement('tr');
                     // colSpan reduced by 2
                     subTr.className = "subtotal-row bg-gray-100 font-bold text-gray-700 italic border-b border-gray-300";
                     subTr.innerHTML = `
                        <td colspan="3" class="p-2 text-right text-sm">Tổng ${item.type} (${item.thick}mm):</td>
                        <td class="p-2 text-right text-blue-800 text-sm">${volumeFormatter.format(groupVol)}</td>
                        <td class="action-col no-print"></td>
                    `;
                    tbody.appendChild(subTr);
                }
            });

            // ADDED: Create Total Row dynamically at the end of tbody instead of using tfoot
            const grandTotalTr = document.createElement('tr');
            grandTotalTr.className = "bg-gray-100 border-t-2 border-gray-300 font-bold text-gray-800";
            // colSpan reduced by 2
            grandTotalTr.innerHTML = `
                <td colspan="3" class="p-3 text-right uppercase">Tổng cộng tất cả:</td>
                <td class="p-3 text-right text-blue-700 text-lg">${volumeFormatter.format(totalVol)}</td>
                <td class="action-col no-print"></td>
            `;
            tbody.appendChild(grandTotalTr);
        }

        function renderSummary() {
            const container = document.getElementById('summaryContainer');
            container.innerHTML = '';

            // Group by Type
            const summary = {};
            items.forEach(item => {
                if (!summary[item.type]) {
                    summary[item.type] = { count: 0, volume: 0, items: [] };
                }
                summary[item.type].count++;
                summary[item.type].volume += item.volume;
            });

            // Render Cards
            for (const [type, data] of Object.entries(summary)) {
                const card = document.createElement('div');
                card.className = "bg-white p-4 rounded-lg shadow-sm border border-gray-200 break-inside-avoid";
                card.innerHTML = `
                    <div class="flex justify-between items-center mb-2">
                        <h4 class="font-bold text-blue-800 text-lg">${type}</h4>
                        <span class="bg-blue-100 text-blue-800 text-xs px-2 py-1 rounded-full">${data.count} tấm</span>
                    </div>
                    <div class="flex justify-between items-end border-t pt-2 mt-2">
                        <span class="text-gray-500 text-sm">Tổng khối lượng:</span>
                        <span class="text-xl font-bold text-gray-800">${volumeFormatter.format(data.volume)} <span class="text-sm font-normal text-gray-500">m³</span></span>
                    </div>
                `;
                container.appendChild(card);
            }
        }
        
        // Keyboard shortcuts
        document.addEventListener('keydown', function(event) {
            if (event.ctrlKey && event.key === 'z') {
                event.preventDefault();
                undo();
            }
        });

    </script>
</body>
</html>
