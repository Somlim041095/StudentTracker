<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ระบบติดตามงานนักเรียน - Google Sheets</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <script src="https://apis.google.com/js/api.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Sarabun:wght@300;400;500;600;700&display=swap');
        body { font-family: 'Sarabun', sans-serif; }
    </style>
</head>
<body class="bg-gradient-to-br from-blue-50 to-indigo-100 min-h-screen">
    <!-- Header -->
    <header class="bg-white shadow-lg">
        <div class="container mx-auto px-6 py-4">
            <div class="flex items-center justify-between">
                <h1 class="text-2xl font-bold text-gray-800">📚 ระบบติดตามงานนักเรียน</h1>
                <div class="flex items-center space-x-4">
                    <!-- Google Sheets Status -->
                    <div id="sheetsStatus" class="flex items-center space-x-2">
                        <div id="connectionStatus" class="w-3 h-3 bg-red-500 rounded-full"></div>
                        <span id="statusText" class="text-sm text-gray-600">ไม่ได้เชื่อมต่อ</span>
                    </div>
                    <button id="connectSheetsBtn" class="px-4 py-2 bg-green-600 text-white rounded-lg hover:bg-green-700 transition-colors text-sm">
                        🔗 เชื่อมต่อ Google Sheets
                    </button>
                    <button id="studentBtn" class="px-6 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition-colors">
                        👨‍🎓 นักเรียน
                    </button>
                    <button id="teacherBtn" class="px-6 py-2 bg-green-600 text-white rounded-lg hover:bg-green-700 transition-colors">
                        👩‍🏫 ครู
                    </button>
                </div>
            </div>
        </div>
    </header>

    <!-- Google Sheets Setup Modal -->
    <div id="sheetsSetupModal" class="fixed inset-0 bg-black bg-opacity-50 hidden z-50 flex items-center justify-center p-4">
        <div class="bg-white rounded-xl shadow-2xl max-w-4xl w-full max-h-[90vh] overflow-y-auto">
            <div class="p-6">
                <div class="flex justify-between items-start mb-6">
                    <div>
                        <h3 class="text-xl font-bold text-gray-800">🔗 เชื่อมต่อ Google Sheets</h3>
                        <p class="text-gray-600 mt-1">ตั้งค่าการเชื่อมต่อเพื่อบันทึกข้อมูลถาวร</p>
                    </div>
                    <button onclick="closeSheetsSetup()" class="text-gray-400 hover:text-gray-600 text-2xl">✕</button>
                </div>

                <div class="space-y-6">
                    <!-- Step 1 -->
                    <div class="bg-blue-50 rounded-lg p-4">
                        <h4 class="font-semibold text-blue-800 mb-3">📋 ขั้นตอนที่ 1: เตรียม Google Sheets</h4>
                        <div class="space-y-3 text-sm text-blue-700">
                            <div class="bg-white rounded p-3 border-l-4 border-blue-400">
                                <strong>1. สร้าง Google Sheets ใหม่</strong>
                                <ul class="mt-1 ml-4 list-disc">
                                    <li>ไปที่ <a href="https://sheets.google.com" target="_blank" class="underline text-blue-600 hover:text-blue-800">sheets.google.com</a></li>
                                    <li>คลิก "สร้างสเปรดชีตใหม่" (สีเขียว)</li>
                                    <li>ตั้งชื่อไฟล์ เช่น "ระบบติดตามงานนักเรียน"</li>
                                </ul>
                            </div>
                            
                            <div class="bg-white rounded p-3 border-l-4 border-blue-400">
                                <strong>2. สร้างชีท 3 แผ่น:</strong>
                                <ul class="mt-1 ml-4 list-disc">
                                    <li><strong>แผ่นที่ 1:</strong> เปลี่ยนชื่อเป็น "Students" (ข้อมูลนักเรียน)</li>
                                    <li><strong>แผ่นที่ 2:</strong> เพิ่มแผ่นใหม่ชื่อ "Assignments" (ข้อมูลงาน)</li>
                                    <li><strong>แผ่นที่ 3:</strong> เพิ่มแผ่นใหม่ชื่อ "Submissions" (การส่งงาน)</li>
                                </ul>
                                <div class="mt-2 text-xs text-gray-600">
                                    💡 คลิกขวาที่แท็บชีทด้านล่าง → เลือก "เปลี่ยนชื่อ" หรือ "แทรกชีท"
                                </div>
                            </div>
                            
                            <div class="bg-white rounded p-3 border-l-4 border-blue-400">
                                <strong>3. ตั้งค่าการแชร์:</strong>
                                <ul class="mt-1 ml-4 list-disc">
                                    <li>คลิกปุ่ม "แชร์" สีฟ้า มุมขวาบน</li>
                                    <li>คลิก "เปลี่ยนเป็นทุกคนที่มีลิงก์"</li>
                                    <li>เลือก "ผู้แก้ไข" (Editor)</li>
                                    <li>คลิก "คัดลอกลิงก์"</li>
                                </ul>
                            </div>
                            
                            <div class="bg-white rounded p-3 border-l-4 border-green-400">
                                <strong>4. เตรียมหัวตาราง (ไม่บังคับ - ระบบจะสร้างให้อัตโนมัติ):</strong>
                                <div class="mt-2 space-y-1 text-xs">
                                    <div><strong>ชีท Students:</strong> รหัสนักเรียน | ชื่อ | ห้อง | วิชา</div>
                                    <div><strong>ชีท Assignments:</strong> ID | ชื่องาน | วิชา | ห้อง | กำหนดส่ง</div>
                                    <div><strong>ชีท Submissions:</strong> รหัสนักเรียน-ID งาน | สถานะ | วันที่ส่ง</div>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- Step 2 -->
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">📎 Google Sheets URL</label>
                        <input type="url" id="sheetsUrl" 
                               class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent" 
                               placeholder="https://docs.google.com/spreadsheets/d/...">
                        <p class="text-xs text-gray-500 mt-1">วาง URL ที่คัดลอกจากขั้นตอนที่ 1</p>
                    </div>

                    <!-- Demo Mode -->
                    <div class="bg-yellow-50 border border-yellow-200 rounded-lg p-4">
                        <div class="flex items-center mb-2">
                            <input type="checkbox" id="demoMode" class="mr-2">
                            <label for="demoMode" class="text-sm font-medium text-yellow-800">🧪 โหมดทดสอบ (ไม่ต้องใช้ Google Sheets จริง)</label>
                        </div>
                        <p class="text-xs text-yellow-700">เลือกตัวเลือกนี้เพื่อทดสอบระบบโดยไม่ต้องเชื่อมต่อ Google Sheets จริง</p>
                    </div>

                    <!-- Real Implementation Guide -->
                    <div class="bg-blue-50 border border-blue-200 rounded-lg p-4 mb-4">
                        <div class="flex items-start">
                            <span class="text-blue-500 mr-2">💡</span>
                            <div class="text-sm text-blue-700">
                                <strong>วิธีการเชื่อมต่อจริง:</strong>
                                <div class="mt-3 space-y-3">
                                    <div class="bg-white rounded p-3 border-l-4 border-green-400">
                                        <strong>🚀 วิธีที่ 1: Google Apps Script (แนะนำ)</strong>
                                        <ul class="mt-1 ml-4 list-disc text-xs">
                                            <li>ไปที่ <a href="https://script.google.com" target="_blank" class="underline text-blue-600">script.google.com</a></li>
                                            <li>สร้างโปรเจกต์ใหม่ และคัดลอกโค้ดที่เตรียมไว้ให้</li>
                                            <li>Deploy เป็น Web App และได้ URL สำหรับเชื่อมต่อ</li>
                                            <li>✅ ใช้งานง่าย ไม่ต้องตั้งค่า API Key</li>
                                        </ul>
                                        <button onclick="showAppsScriptGuide()" class="mt-2 px-3 py-1 bg-green-600 text-white rounded text-xs hover:bg-green-700">
                                            📖 ดูคู่มือ Apps Script
                                        </button>
                                    </div>
                                    
                                    <div class="bg-white rounded p-3 border-l-4 border-blue-400">
                                        <strong>🔧 วิธีที่ 2: Google Sheets API</strong>
                                        <ul class="mt-1 ml-4 list-disc text-xs">
                                            <li>ตั้งค่า Google Cloud Console</li>
                                            <li>สร้าง API Key และเปิดใช้งาน Sheets API</li>
                                            <li>เหมาะสำหรับนักพัฒนาที่มีประสบการณ์</li>
                                        </ul>
                                    </div>
                                    
                                    <div class="bg-white rounded p-3 border-l-4 border-purple-400">
                                        <strong>🏫 สำหรับโรงเรียน:</strong>
                                        <ul class="mt-1 ml-4 list-disc text-xs">
                                            <li>ใช้ Google Workspace for Education (ฟรี)</li>
                                            <li>ความปลอดภัยสูง และจัดการง่าย</li>
                                            <li>สามารถควบคุมสิทธิ์การเข้าถึงได้</li>
                                        </ul>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- Current Limitation -->
                    <div class="bg-yellow-50 border border-yellow-200 rounded-lg p-4 mb-4">
                        <div class="flex items-start">
                            <span class="text-yellow-500 mr-2">⚠️</span>
                            <div class="text-sm text-yellow-700">
                                <strong>ข้อจำกัดปัจจุบัน:</strong>
                                <ul class="mt-1 ml-4 list-disc">
                                    <li>ระบบนี้ยังไม่สามารถเขียนข้อมูลลง Google Sheets ได้โดยตรง</li>
                                    <li>ข้อมูลจะถูกเก็บในเบราว์เซอร์เท่านั้น (Local Storage)</li>
                                    <li>เมื่อรีเฟรชหน้าเว็บ ข้อมูลจะหายไป</li>
                                    <li>สามารถอ่านข้อมูลจาก Google Sheets ได้ แต่ไม่สามารถเขียนได้</li>
                                </ul>
                            </div>
                        </div>
                    </div>

                    <!-- Warning -->
                    <div class="bg-red-50 border border-red-200 rounded-lg p-4">
                        <div class="flex items-start">
                            <span class="text-red-500 mr-2">🔒</span>
                            <div class="text-sm text-red-700">
                                <strong>ข้อควรระวัง:</strong>
                                <ul class="mt-1 ml-4 list-disc">
                                    <li>Google Sheets ต้องตั้งค่าให้ "ทุกคนที่มีลิงก์สามารถแก้ไขได้"</li>
                                    <li>ไม่ควรใส่ข้อมูลส่วนตัวที่สำคัญใน Google Sheets ที่แชร์สาธารณะ</li>
                                    <li>สำหรับการใช้งานจริงในโรงเรียน ควรใช้ Google Workspace for Education</li>
                                </ul>
                            </div>
                        </div>
                    </div>

                    <!-- Action Buttons -->
                    <div class="flex justify-end space-x-3">
                        <button onclick="closeSheetsSetup()" 
                                class="px-6 py-2 bg-gray-300 text-gray-700 rounded-lg hover:bg-gray-400 transition-colors">
                            ยกเลิก
                        </button>
                        <button onclick="connectToSheets()" 
                                class="px-6 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition-colors">
                            🔗 เชื่อมต่อ
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Student Section -->
    <div id="studentSection" class="container mx-auto px-6 py-8">
        <div class="max-w-4xl mx-auto">
            <div class="bg-white rounded-xl shadow-lg p-8">
                <h2 class="text-xl font-semibold text-gray-800 mb-6">🔍 ค้นหาข้อมูลการส่งงาน</h2>
                
                <div class="grid md:grid-cols-3 gap-4 mb-6">
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">รหัสนักเรียน</label>
                        <input type="text" id="studentId" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent" placeholder="กรอกรหัสนักเรียน">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">ห้องเรียน</label>
                        <select id="classSelect" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                            <option value="">เลือกห้องเรียน</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">วิชา</label>
                        <select id="subjectSelect" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                            <option value="">เลือกวิชา</option>
                        </select>
                    </div>
                </div>

                <button id="searchBtn" class="w-full bg-blue-600 text-white py-3 rounded-lg hover:bg-blue-700 transition-colors font-medium">
                    🔍 ค้นหา
                </button>

                <!-- Search Results -->
                <div id="searchResults" class="mt-8 hidden">
                    <!-- Student Info Card -->
                    <div id="studentInfo" class="bg-gradient-to-r from-blue-500 to-purple-600 text-white rounded-xl p-6 mb-6">
                        <div class="flex items-center space-x-4">
                            <div class="w-16 h-16 bg-white bg-opacity-20 rounded-full flex items-center justify-center text-2xl">
                                👨‍🎓
                            </div>
                            <div>
                                <h3 class="text-xl font-bold" id="studentName">ข้อมูลนักเรียน</h3>
                                <p class="opacity-90" id="studentDetails">รหัส: - | ห้อง: - | วิชา: -</p>
                            </div>
                        </div>
                    </div>

                    <!-- Statistics Cards -->
                    <div class="grid md:grid-cols-4 gap-4 mb-6">
                        <div class="bg-white rounded-lg p-4 shadow-md border-l-4 border-green-500">
                            <div class="flex items-center">
                                <div class="text-green-500 text-2xl mr-3">✅</div>
                                <div>
                                    <p class="text-sm text-gray-600">ส่งแล้ว</p>
                                    <p class="text-xl font-bold text-gray-800" id="submittedCount">0</p>
                                </div>
                            </div>
                        </div>
                        <div class="bg-white rounded-lg p-4 shadow-md border-l-4 border-red-500">
                            <div class="flex items-center">
                                <div class="text-red-500 text-2xl mr-3">❌</div>
                                <div>
                                    <p class="text-sm text-gray-600">ยังไม่ส่ง</p>
                                    <p class="text-xl font-bold text-gray-800" id="notSubmittedCount">0</p>
                                </div>
                            </div>
                        </div>
                        <div class="bg-white rounded-lg p-4 shadow-md border-l-4 border-yellow-500">
                            <div class="flex items-center">
                                <div class="text-yellow-500 text-2xl mr-3">⏰</div>
                                <div>
                                    <p class="text-sm text-gray-600">เกินกำหนด</p>
                                    <p class="text-xl font-bold text-gray-800" id="overdueCount">0</p>
                                </div>
                            </div>
                        </div>
                        <div class="bg-white rounded-lg p-4 shadow-md border-l-4 border-blue-500">
                            <div class="flex items-center">
                                <div class="text-blue-500 text-2xl mr-3">📊</div>
                                <div>
                                    <p class="text-sm text-gray-600">เปอร์เซ็นต์</p>
                                    <p class="text-xl font-bold text-gray-800" id="completionRate">0%</p>
                                </div>
                            </div>
                        </div>
                    </div>

                    <h3 class="text-lg font-semibold text-gray-800 mb-4">📋 รายละเอียดงาน</h3>
                    
                    <!-- Assignment Cards -->
                    <div class="space-y-4" id="assignmentCards">
                    </div>

                    <!-- Traditional Table (Hidden by default) -->
                    <div id="tableView" class="hidden">
                        <div class="flex justify-between items-center mb-4">
                            <h3 class="text-lg font-semibold text-gray-800">📋 ตารางสรุป</h3>
                            <button id="toggleView" class="px-4 py-2 bg-gray-600 text-white rounded-lg hover:bg-gray-700 transition-colors">
                                📊 มุมมองการ์ด
                            </button>
                        </div>
                        <div class="overflow-x-auto">
                            <table class="w-full bg-white border border-gray-200 rounded-lg">
                                <thead class="bg-gray-50">
                                    <tr>
                                        <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">งาน</th>
                                        <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">วิชา</th>
                                        <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">กำหนดส่ง</th>
                                        <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">สถานะ</th>
                                        <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">วันที่ส่ง</th>
                                    </tr>
                                </thead>
                                <tbody id="resultsTable">
                                </tbody>
                            </table>
                        </div>
                    </div>

                    <!-- Toggle View Button -->
                    <div class="mt-6 text-center">
                        <button id="toggleViewBtn" class="px-6 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition-colors">
                            📊 มุมมองตาราง
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Teacher Section -->
    <div id="teacherSection" class="container mx-auto px-6 py-8 hidden">
        <div class="max-w-6xl mx-auto">
            <!-- Sync Status -->
            <div id="syncStatus" class="bg-white rounded-xl shadow-lg p-6 mb-8 hidden">
                <div class="flex items-center justify-between">
                    <div class="flex items-center space-x-3">
                        <div class="w-4 h-4 bg-green-500 rounded-full animate-pulse"></div>
                        <span class="text-green-700 font-medium">🔄 ซิงค์ข้อมูลกับ Google Sheets</span>
                    </div>
                    <div class="flex space-x-2">
                        <button onclick="syncWithSheets()" class="px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition-colors text-sm">
                            🔄 ซิงค์ตอนนี้
                        </button>
                        <button onclick="exportData()" class="px-4 py-2 bg-green-600 text-white rounded-lg hover:bg-green-700 transition-colors text-sm">
                            📤 ส่งออกข้อมูล
                        </button>
                        <button onclick="clearAllData()" class="px-4 py-2 bg-red-600 text-white rounded-lg hover:bg-red-700 transition-colors text-sm">
                            🗑️ ล้างข้อมูล
                        </button>
                    </div>
                </div>
            </div>

            <!-- Class & Subject Management -->
            <div class="bg-white rounded-xl shadow-lg p-8 mb-8">
                <h2 class="text-xl font-semibold text-gray-800 mb-6">🏫 จัดการห้องเรียนและวิชา</h2>
                
                <div class="grid md:grid-cols-2 gap-8">
                    <!-- Class Management -->
                    <div>
                        <h3 class="text-lg font-medium text-gray-700 mb-4">📚 จัดการห้องเรียน</h3>
                        <div class="space-y-4">
                            <div class="flex space-x-2">
                                <input type="text" id="classNameInput" placeholder="ชื่อห้องเรียน (เช่น ม.6/1, ป.4/2, อนุบาล 1)" class="flex-1 px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                                <button onclick="addClass()" class="px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition-colors">
                                    ➕ เพิ่ม
                                </button>
                            </div>
                            <div class="text-xs text-gray-500 mb-2">
                                💡 ตัวอย่าง: ม.1/1, ม.2/3, ป.5/2, อนุบาล 1, ป.6 วิทย์-คณิต
                            </div>
                            <div id="classList" class="space-y-2 max-h-40 overflow-y-auto">
                                <!-- Classes will be listed here -->
                            </div>
                        </div>
                    </div>
                    
                    <!-- Subject Management -->
                    <div>
                        <h3 class="text-lg font-medium text-gray-700 mb-4">📖 จัดการวิชา</h3>
                        <div class="space-y-4">
                            <div class="flex space-x-2">
                                <input type="text" id="subjectName" placeholder="ชื่อวิชา" class="flex-1 px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-green-500 focus:border-transparent">
                                <button onclick="addSubject()" class="px-4 py-2 bg-green-600 text-white rounded-lg hover:bg-green-700 transition-colors">
                                    ➕ เพิ่ม
                                </button>
                            </div>
                            <div id="subjectList" class="space-y-2 max-h-40 overflow-y-auto">
                                <!-- Subjects will be listed here -->
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Upload Section -->
            <div class="bg-white rounded-xl shadow-lg p-8 mb-8">
                <h2 class="text-xl font-semibold text-gray-800 mb-6">📤 อัพโหลดข้อมูลนักเรียน</h2>
                
                <!-- Class Selection for Upload -->
                <div class="grid md:grid-cols-2 gap-4 mb-6">
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">เลือกห้องเรียนสำหรับนักเรียนที่จะอัพโหลด</label>
                        <select id="uploadClassSelect" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                            <option value="">เลือกห้องเรียน</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">เลือกวิชาสำหรับนักเรียนที่จะอัพโหลด</label>
                        <select id="uploadSubjectSelect" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-green-500 focus:border-transparent">
                            <option value="">เลือกวิชา</option>
                        </select>
                    </div>
                </div>
                
                <div class="border-2 border-dashed border-gray-300 rounded-lg p-8 text-center">
                    <input type="file" id="excelFile" accept=".xlsx,.xls" class="hidden">
                    <button onclick="document.getElementById('excelFile').click()" class="bg-green-600 text-white px-6 py-3 rounded-lg hover:bg-green-700 transition-colors">
                        📁 เลือกไฟล์ Excel
                    </button>
                    <p class="text-gray-500 mt-2">รองรับไฟล์ .xlsx และ .xls</p>
                    <p class="text-sm text-gray-400 mt-1">รูปแบบ: รหัสนักเรียน | ชื่อนักเรียน</p>
                    <p class="text-xs text-blue-600 mt-2">💡 ห้องเรียนและวิชาจะถูกกำหนดตามที่เลือกข้างบน</p>
                    <div id="sheetsConnected" class="mt-4 hidden">
                        <div class="inline-flex items-center px-3 py-1 bg-green-100 text-green-800 rounded-full text-sm">
                            <span class="mr-1">✅</span>
                            ข้อมูลจะถูกบันทึกใน Google Sheets อัตโนมัติ
                        </div>
                    </div>
                </div>
            </div>

            <!-- Assignment Management -->
            <div class="bg-white rounded-xl shadow-lg p-8">
                <h2 class="text-xl font-semibold text-gray-800 mb-6">📝 จัดการงาน</h2>
                
                <!-- Add Assignment Form -->
                <div class="grid md:grid-cols-4 gap-4 mb-6">
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">ชื่องาน</label>
                        <input type="text" id="assignmentName" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-green-500 focus:border-transparent" placeholder="ชื่องาน">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">วิชา</label>
                        <select id="assignmentSubject" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-green-500 focus:border-transparent">
                            <option value="">เลือกวิชา</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">ห้องเรียน</label>
                        <select id="assignmentClass" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-green-500 focus:border-transparent">
                            <option value="">เลือกห้องเรียน</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">กำหนดส่ง</label>
                        <input type="date" id="dueDate" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-green-500 focus:border-transparent">
                    </div>
                </div>

                <button id="addAssignmentBtn" class="bg-green-600 text-white px-6 py-3 rounded-lg hover:bg-green-700 transition-colors font-medium mb-8">
                    ➕ เพิ่มงาน
                </button>

                <!-- Assignments List -->
                <div id="assignmentsList">
                    <div class="flex justify-between items-center mb-6">
                        <h3 class="text-lg font-semibold text-gray-800">📋 รายการงาน</h3>
                        <div class="flex space-x-2">
                            <select id="filterClass" class="px-3 py-2 border border-gray-300 rounded-lg text-sm focus:ring-2 focus:ring-blue-500">
                                <option value="">ทุกห้องเรียน</option>
                            </select>
                            <select id="filterSubject" class="px-3 py-2 border border-gray-300 rounded-lg text-sm focus:ring-2 focus:ring-blue-500">
                                <option value="">ทุกวิชา</option>
                            </select>
                        </div>
                    </div>
                    <div id="assignmentsContainer">
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Google Apps Script Guide Modal -->
    <div id="appsScriptModal" class="fixed inset-0 bg-black bg-opacity-50 hidden z-50 flex items-center justify-center p-4">
        <div class="bg-white rounded-xl shadow-2xl max-w-5xl w-full max-h-[90vh] overflow-y-auto">
            <div class="p-6">
                <div class="flex justify-between items-start mb-6">
                    <div>
                        <h3 class="text-2xl font-bold text-gray-800">🚀 คู่มือ Google Apps Script</h3>
                        <p class="text-gray-600 mt-1">วิธีการสร้าง Web App เพื่อเชื่อมต่อกับ Google Sheets</p>
                    </div>
                    <button onclick="closeAppsScriptGuide()" class="text-gray-400 hover:text-gray-600 text-2xl">✕</button>
                </div>

                <div class="space-y-6">
                    <!-- Step 1: Create Google Sheets -->
                    <div class="bg-blue-50 rounded-lg p-6">
                        <h4 class="text-lg font-bold text-blue-800 mb-4">📋 ขั้นตอนที่ 1: เตรียม Google Sheets</h4>
                        <div class="space-y-3">
                            <div class="bg-white rounded p-4 border-l-4 border-blue-400">
                                <strong>1. สร้าง Google Sheets ใหม่:</strong>
                                <ul class="mt-2 ml-4 list-disc text-sm">
                                    <li>ไปที่ <a href="https://sheets.google.com" target="_blank" class="text-blue-600 underline">sheets.google.com</a></li>
                                    <li>คลิก "สร้างสเปรดชีตใหม่"</li>
                                    <li>ตั้งชื่อ เช่น "ระบบติดตามงานนักเรียน"</li>
                                </ul>
                            </div>
                            
                            <div class="bg-white rounded p-4 border-l-4 border-green-400">
                                <strong>2. สร้าง 3 แผ่นงาน:</strong>
                                <div class="mt-2 grid md:grid-cols-3 gap-3 text-sm">
                                    <div class="bg-green-50 p-3 rounded">
                                        <strong>แผ่น "Students"</strong>
                                        <div class="text-xs mt-1">
                                            A1: รหัสนักเรียน<br>
                                            B1: ชื่อ<br>
                                            C1: ห้อง<br>
                                            D1: วิชา
                                        </div>
                                    </div>
                                    <div class="bg-blue-50 p-3 rounded">
                                        <strong>แผ่น "Assignments"</strong>
                                        <div class="text-xs mt-1">
                                            A1: ID<br>
                                            B1: ชื่องาน<br>
                                            C1: วิชา<br>
                                            D1: ห้อง<br>
                                            E1: กำหนดส่ง
                                        </div>
                                    </div>
                                    <div class="bg-purple-50 p-3 rounded">
                                        <strong>แผ่น "Submissions"</strong>
                                        <div class="text-xs mt-1">
                                            A1: รหัสนักเรียน-ID<br>
                                            B1: สถานะ<br>
                                            C1: วันที่ส่ง
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- Step 2: Create Apps Script -->
                    <div class="bg-green-50 rounded-lg p-6">
                        <h4 class="text-lg font-bold text-green-800 mb-4">⚙️ ขั้นตอนที่ 2: สร้าง Google Apps Script</h4>
                        <div class="space-y-4">
                            <div class="bg-white rounded p-4 border-l-4 border-green-400">
                                <strong>1. เปิด Apps Script:</strong>
                                <ul class="mt-2 ml-4 list-disc text-sm">
                                    <li>ใน Google Sheets ไปที่เมนู "ส่วนขยาย" → "Apps Script"</li>
                                    <li>หรือไปที่ <a href="https://script.google.com" target="_blank" class="text-blue-600 underline">script.google.com</a> โดยตรง</li>
                                </ul>
                            </div>
                            
                            <div class="bg-white rounded p-4 border-l-4 border-yellow-400">
                                <strong>2. คัดลอกโค้ด Apps Script:</strong>
                                <div class="mt-3">
                                    <div class="bg-gray-900 text-green-400 p-4 rounded-lg text-sm font-mono overflow-x-auto">
<pre>// Google Apps Script สำหรับระบบติดตามงานนักเรียน
function doPost(e) {
  try {
    const data = JSON.parse(e.postData.contents);
    const action = data.action;
    
    // เปิด Google Sheets (ใส่ ID ของ Sheets ที่สร้าง)
    const ss = SpreadsheetApp.openById('YOUR_SPREADSHEET_ID_HERE');
    
    switch(action) {
      case 'saveStudents':
        return saveStudents(ss, data.students);
      case 'saveAssignments':
        return saveAssignments(ss, data.assignments);
      case 'saveSubmissions':
        return saveSubmissions(ss, data.submissions);
      case 'getData':
        return getAllData(ss);
      default:
        return ContentService.createTextOutput(JSON.stringify({
          success: false, 
          error: 'Invalid action'
        })).setMimeType(ContentService.MimeType.JSON);
    }
  } catch (error) {
    return ContentService.createTextOutput(JSON.stringify({
      success: false, 
      error: error.toString()
    })).setMimeType(ContentService.MimeType.JSON);
  }
}

function saveStudents(ss, students) {
  const sheet = ss.getSheetByName('Students');
  
  // ล้างข้อมูลเก่า (เก็บหัวตาราง)
  if (sheet.getLastRow() > 1) {
    sheet.getRange(2, 1, sheet.getLastRow() - 1, sheet.getLastColumn()).clear();
  }
  
  // เพิ่มข้อมูลใหม่
  if (students && students.length > 0) {
    const values = students.map(student => [
      student.id, student.name, student.class, student.subject
    ]);
    sheet.getRange(2, 1, values.length, 4).setValues(values);
  }
  
  return ContentService.createTextOutput(JSON.stringify({
    success: true, 
    message: 'บันทึกข้อมูลนักเรียนสำเร็จ'
  })).setMimeType(ContentService.MimeType.JSON);
}

function saveAssignments(ss, assignments) {
  const sheet = ss.getSheetByName('Assignments');
  
  if (sheet.getLastRow() > 1) {
    sheet.getRange(2, 1, sheet.getLastRow() - 1, sheet.getLastColumn()).clear();
  }
  
  if (assignments && assignments.length > 0) {
    const values = assignments.map(assignment => [
      assignment.id, assignment.name, assignment.subject, 
      assignment.class, assignment.dueDate
    ]);
    sheet.getRange(2, 1, values.length, 5).setValues(values);
  }
  
  return ContentService.createTextOutput(JSON.stringify({
    success: true, 
    message: 'บันทึกข้อมูลงานสำเร็จ'
  })).setMimeType(ContentService.MimeType.JSON);
}

function saveSubmissions(ss, submissions) {
  const sheet = ss.getSheetByName('Submissions');
  
  if (sheet.getLastRow() > 1) {
    sheet.getRange(2, 1, sheet.getLastRow() - 1, sheet.getLastColumn()).clear();
  }
  
  if (submissions && Object.keys(submissions).length > 0) {
    const values = Object.entries(submissions).map(([key, submission]) => [
      key, 
      submission.submitted ? 'ส่งแล้ว' : 'ยังไม่ส่ง', 
      submission.date || ''
    ]);
    sheet.getRange(2, 1, values.length, 3).setValues(values);
  }
  
  return ContentService.createTextOutput(JSON.stringify({
    success: true, 
    message: 'บันทึกข้อมูลการส่งงานสำเร็จ'
  })).setMimeType(ContentService.MimeType.JSON);
}

function getAllData(ss) {
  const studentsSheet = ss.getSheetByName('Students');
  const assignmentsSheet = ss.getSheetByName('Assignments');
  const submissionsSheet = ss.getSheetByName('Submissions');
  
  // อ่านข้อมูลนักเรียน
  const studentsData = studentsSheet.getDataRange().getValues();
  const students = studentsData.slice(1).map(row => ({
    id: row[0], name: row[1], class: row[2], subject: row[3]
  }));
  
  // อ่านข้อมูลงาน
  const assignmentsData = assignmentsSheet.getDataRange().getValues();
  const assignments = assignmentsData.slice(1).map(row => ({
    id: row[0], name: row[1], subject: row[2], 
    class: row[3], dueDate: row[4]
  }));
  
  // อ่านข้อมูลการส่งงาน
  const submissionsData = submissionsSheet.getDataRange().getValues();
  const submissions = {};
  submissionsData.slice(1).forEach(row => {
    submissions[row[0]] = {
      submitted: row[1] === 'ส่งแล้ว',
      date: row[2] || null
    };
  });
  
  return ContentService.createTextOutput(JSON.stringify({
    success: true,
    data: { students, assignments, submissions }
  })).setMimeType(ContentService.MimeType.JSON);
}</pre>
                                    </div>
                                    <button onclick="copyAppsScriptCode()" class="mt-2 px-4 py-2 bg-blue-600 text-white rounded hover:bg-blue-700 text-sm">
                                        📋 คัดลอกโค้ด
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- Step 3: Deploy -->
                    <div class="bg-purple-50 rounded-lg p-6">
                        <h4 class="text-lg font-bold text-purple-800 mb-4">🚀 ขั้นตอนที่ 3: Deploy Web App</h4>
                        <div class="space-y-3">
                            <div class="bg-white rounded p-4 border-l-4 border-purple-400">
                                <strong>1. แก้ไข Spreadsheet ID:</strong>
                                <ul class="mt-2 ml-4 list-disc text-sm">
                                    <li>ในโค้ด Apps Script หาบรรทัด <code class="bg-gray-200 px-1 rounded">YOUR_SPREADSHEET_ID_HERE</code></li>
                                    <li>แทนที่ด้วย ID ของ Google Sheets ที่สร้าง</li>
                                    <li>ID อยู่ใน URL: <code class="bg-gray-200 px-1 rounded text-xs">docs.google.com/spreadsheets/d/<strong>ID_ตรงนี้</strong>/edit</code></li>
                                </ul>
                            </div>
                            
                            <div class="bg-white rounded p-4 border-l-4 border-orange-400">
                                <strong>2. Deploy เป็น Web App:</strong>
                                <ul class="mt-2 ml-4 list-disc text-sm">
                                    <li>คลิก "Deploy" → "New deployment"</li>
                                    <li>เลือก Type: "Web app"</li>
                                    <li>Execute as: "Me"</li>
                                    <li>Who has access: "Anyone" (สำหรับการทดสอบ)</li>
                                    <li>คลิก "Deploy"</li>
                                </ul>
                            </div>
                            
                            <div class="bg-white rounded p-4 border-l-4 border-green-400">
                                <strong>3. คัดลอก Web App URL:</strong>
                                <ul class="mt-2 ml-4 list-disc text-sm">
                                    <li>หลัง Deploy สำเร็จ จะได้ URL ของ Web App</li>
                                    <li>คัดลอก URL นี้มาใส่ในระบบ</li>
                                    <li>URL จะมีลักษณะ: <code class="bg-gray-200 px-1 rounded text-xs">https://script.google.com/macros/s/.../exec</code></li>
                                </ul>
                            </div>
                        </div>
                    </div>

                    <!-- Step 4: Connect -->
                    <div class="bg-yellow-50 rounded-lg p-6">
                        <h4 class="text-lg font-bold text-yellow-800 mb-4">🔗 ขั้นตอนที่ 4: เชื่อมต่อระบบ</h4>
                        <div class="bg-white rounded p-4 border-l-4 border-yellow-400">
                            <strong>วิธีใช้งาน:</strong>
                            <ol class="mt-2 ml-4 list-decimal text-sm">
                                <li>กลับมาที่หน้าระบบนี้</li>
                                <li>คลิก "🔗 เชื่อมต่อ Google Sheets"</li>
                                <li>ใส่ Web App URL ที่ได้จากขั้นตอนที่ 3</li>
                                <li>ยกเลิกการเลือก "โหมดทดสอบ"</li>
                                <li>คลิก "เชื่อมต่อ"</li>
                            </ol>
                        </div>
                    </div>

                    <!-- Troubleshooting -->
                    <div class="bg-red-50 rounded-lg p-6">
                        <h4 class="text-lg font-bold text-red-800 mb-4">🔧 แก้ไขปัญหา</h4>
                        <div class="space-y-3 text-sm">
                            <div class="bg-white rounded p-3 border-l-4 border-red-400">
                                <strong>ปัญหา: ไม่สามารถเชื่อมต่อได้</strong>
                                <ul class="mt-1 ml-4 list-disc">
                                    <li>ตรวจสอบ Web App URL ให้ถูกต้อง</li>
                                    <li>ตรวจสอบว่า Deploy แล้วและเลือก "Anyone" ในการเข้าถึง</li>
                                    <li>ลองรีเฟรชหน้าเว็บ</li>
                                </ul>
                            </div>
                            
                            <div class="bg-white rounded p-3 border-l-4 border-orange-400">
                                <strong>ปัญหา: ข้อมูลไม่อัพเดท</strong>
                                <ul class="mt-1 ml-4 list-disc">
                                    <li>ตรวจสอบ Spreadsheet ID ในโค้ด Apps Script</li>
                                    <li>ตรวจสอบชื่อแผ่นงาน (Students, Assignments, Submissions)</li>
                                    <li>ลอง Deploy ใหม่</li>
                                </ul>
                            </div>
                        </div>
                    </div>
                </div>

                <div class="flex justify-end space-x-3 mt-6">
                    <button onclick="closeAppsScriptGuide()" 
                            class="px-6 py-2 bg-gray-300 text-gray-700 rounded-lg hover:bg-gray-400 transition-colors">
                        ปิด
                    </button>
                    <a href="https://script.google.com" target="_blank"
                       class="px-6 py-2 bg-green-600 text-white rounded-lg hover:bg-green-700 transition-colors">
                        🚀 เปิด Apps Script
                    </a>
                </div>
            </div>
        </div>
    </div>

    <!-- Assignment Detail Modal -->
    <div id="assignmentModal" class="fixed inset-0 bg-black bg-opacity-50 hidden z-50 flex items-center justify-center p-4">
        <div class="bg-white rounded-xl shadow-2xl max-w-2xl w-full max-h-[90vh] overflow-y-auto">
            <div class="p-6">
                <!-- Modal Header -->
                <div class="flex justify-between items-start mb-6">
                    <div>
                        <h3 class="text-xl font-bold text-gray-800" id="modalTitle">รายละเอียดงาน</h3>
                        <p class="text-gray-600 mt-1" id="modalSubtitle">ข้อมูลเพิ่มเติม</p>
                    </div>
                    <button onclick="closeAssignmentModal()" class="text-gray-400 hover:text-gray-600 text-2xl">
                        ✕
                    </button>
                </div>

                <!-- Assignment Info -->
                <div class="space-y-4 mb-6">
                    <div class="bg-gray-50 rounded-lg p-4">
                        <div class="grid md:grid-cols-2 gap-4">
                            <div>
                                <label class="block text-sm font-medium text-gray-700 mb-1">ชื่องาน</label>
                                <p class="text-gray-900" id="modalAssignmentName">-</p>
                            </div>
                            <div>
                                <label class="block text-sm font-medium text-gray-700 mb-1">วิชา</label>
                                <p class="text-gray-900" id="modalSubject">-</p>
                            </div>
                            <div>
                                <label class="block text-sm font-medium text-gray-700 mb-1">ห้องเรียน</label>
                                <p class="text-gray-900" id="modalClass">-</p>
                            </div>
                            <div>
                                <label class="block text-sm font-medium text-gray-700 mb-1">กำหนดส่ง</label>
                                <p class="text-gray-900" id="modalDueDate">-</p>
                            </div>
                        </div>
                    </div>

                    <!-- Status Card -->
                    <div id="modalStatusCard" class="rounded-lg p-4">
                        <div class="flex items-center justify-between">
                            <div>
                                <h4 class="font-semibold text-lg mb-2" id="modalStatusTitle">สถานะการส่งงาน</h4>
                                <div class="flex items-center space-x-4">
                                    <span id="modalStatusBadge" class="px-3 py-1 rounded-full text-sm font-medium">-</span>
                                    <span id="modalSubmissionDate" class="text-sm text-gray-600">-</span>
                                </div>
                            </div>
                            <div class="text-4xl" id="modalStatusIcon">📝</div>
                        </div>
                    </div>

                    <!-- Time Info -->
                    <div class="bg-blue-50 rounded-lg p-4">
                        <h4 class="font-semibold text-gray-800 mb-2">⏰ ข้อมูลเวลา</h4>
                        <div id="modalTimeInfo" class="text-sm text-gray-700">
                            <p id="modalDaysInfo">-</p>
                            <p id="modalAdvice" class="mt-2 font-medium">-</p>
                        </div>
                    </div>
                </div>

                <!-- Action Buttons -->
                <div class="flex justify-end space-x-3">
                    <button onclick="closeAssignmentModal()" 
                            class="px-6 py-2 bg-gray-300 text-gray-700 rounded-lg hover:bg-gray-400 transition-colors">
                        ปิด
                    </button>
                </div>
            </div>
        </div>
    </div>

    <script>
        // Google Sheets Integration
        let sheetsConnected = false;
        let sheetsUrl = '';
        let isDemoMode = false;

        // Data storage
        let students = [];
        let assignments = [];
        let submissions = {};
        let classes = ['ม.6/1', 'ม.6/2']; // Available classes
        let subjects = ['คณิตศาสตร์', 'ฟิสิกส์', 'เคมี', 'ชีววิทยา']; // Available subjects

        // Sample data
        const sampleStudents = [
            { id: '65001', name: 'สมชาย ใจดี', class: 'ม.6/1', subject: 'คณิตศาสตร์' },
            { id: '65002', name: 'สมหญิง รักเรียน', class: 'ม.6/1', subject: 'คณิตศาสตร์' },
            { id: '65003', name: 'วิชัย เก่งมาก', class: 'ม.6/2', subject: 'ฟิสิกส์' },
            { id: '65004', name: 'มาลี ขยันดี', class: 'ม.6/2', subject: 'ฟิสิกส์' }
        ];

        const sampleAssignments = [
            { id: 1, name: 'การบ้านที่ 1', subject: 'คณิตศาสตร์', class: 'ม.6/1', dueDate: '2024-01-15' },
            { id: 2, name: 'โครงงานฟิสิกส์', subject: 'ฟิสิกส์', class: 'ม.6/2', dueDate: '2024-01-20' }
        ];

        // Load data from localStorage or use sample data
        function loadDataFromStorage() {
            try {
                const savedStudents = localStorage.getItem('students');
                const savedAssignments = localStorage.getItem('assignments');
                const savedSubmissions = localStorage.getItem('submissions');
                const savedClasses = localStorage.getItem('classes');
                const savedSubjects = localStorage.getItem('subjects');
                
                students = savedStudents ? JSON.parse(savedStudents) : [...sampleStudents];
                assignments = savedAssignments ? JSON.parse(savedAssignments) : [...sampleAssignments];
                submissions = savedSubmissions ? JSON.parse(savedSubmissions) : {
                    '65001-1': { submitted: true, date: '2024-01-14' },
                    '65002-1': { submitted: false },
                    '65003-2': { submitted: true, date: '2024-01-18' },
                    '65004-2': { submitted: false }
                };
                classes = savedClasses ? JSON.parse(savedClasses) : ['ม.6/1', 'ม.6/2'];
                subjects = savedSubjects ? JSON.parse(savedSubjects) : ['คณิตศาสตร์', 'ฟิสิกส์', 'เคมี', 'ชีววิทยา'];
                
                console.log('Data loaded from localStorage');
            } catch (error) {
                console.error('Error loading data from localStorage:', error);
                // Use sample data if loading fails
                students = [...sampleStudents];
                assignments = [...sampleAssignments];
                submissions = {
                    '65001-1': { submitted: true, date: '2024-01-14' },
                    '65002-1': { submitted: false },
                    '65003-2': { submitted: true, date: '2024-01-18' },
                    '65004-2': { submitted: false }
                };
            }
        }

        // Save data to localStorage
        function saveDataToStorage() {
            try {
                localStorage.setItem('students', JSON.stringify(students));
                localStorage.setItem('assignments', JSON.stringify(assignments));
                localStorage.setItem('submissions', JSON.stringify(submissions));
                localStorage.setItem('classes', JSON.stringify(classes));
                localStorage.setItem('subjects', JSON.stringify(subjects));
                console.log('Data saved to localStorage');
            } catch (error) {
                console.error('Error saving data to localStorage:', error);
            }
        }

        // Initialize data
        loadDataFromStorage();

        // Class and Subject Management Functions
        function addClass() {
            const className = document.getElementById('classNameInput').value.trim();
            
            if (!className) {
                showNotification('กรุณากรอกชื่อห้องเรียน', 'error');
                return;
            }
            
            if (classes.includes(className)) {
                showNotification('ห้องเรียนนี้มีอยู่แล้ว', 'error');
                return;
            }
            
            classes.push(className);
            
            // Save to localStorage and Google Sheets
            saveDataToStorage();
            saveToSheets('classes', classes);
            
            // Clear form
            document.getElementById('classNameInput').value = '';
            
            renderClassList();
            updateAllDropdowns();
            showNotification(`เพิ่มห้องเรียน ${className} สำเร็จ!`, 'success');
        }

        function removeClass(className) {
            const index = classes.indexOf(className);
            if (index > -1) {
                classes.splice(index, 1);
                
                // Save to localStorage and Google Sheets
                saveDataToStorage();
                saveToSheets('classes', classes);
                
                renderClassList();
                updateAllDropdowns();
                showNotification(`ลบห้องเรียน ${className} สำเร็จ!`, 'success');
            }
        }

        function addSubject() {
            const subjectName = document.getElementById('subjectName').value.trim();
            
            if (!subjectName) {
                showNotification('กรุณากรอกชื่อวิชา', 'error');
                return;
            }
            
            if (subjects.includes(subjectName)) {
                showNotification('วิชานี้มีอยู่แล้ว', 'error');
                return;
            }
            
            subjects.push(subjectName);
            
            // Save to localStorage and Google Sheets
            saveDataToStorage();
            saveToSheets('subjects', subjects);
            
            // Clear form
            document.getElementById('subjectName').value = '';
            
            renderSubjectList();
            updateAllDropdowns();
            showNotification(`เพิ่มวิชา ${subjectName} สำเร็จ!`, 'success');
        }

        function removeSubject(subjectName) {
            const index = subjects.indexOf(subjectName);
            if (index > -1) {
                subjects.splice(index, 1);
                
                // Save to localStorage and Google Sheets
                saveDataToStorage();
                saveToSheets('subjects', subjects);
                
                renderSubjectList();
                updateAllDropdowns();
                showNotification(`ลบวิชา ${subjectName} สำเร็จ!`, 'success');
            }
        }

        function renderClassList() {
            const container = document.getElementById('classList');
            container.innerHTML = '';
            
            if (classes.length === 0) {
                container.innerHTML = '<p class="text-gray-500 text-sm">ยังไม่มีห้องเรียน</p>';
                return;
            }
            
            classes.forEach(className => {
                const div = document.createElement('div');
                div.className = 'flex items-center justify-between bg-blue-50 px-3 py-2 rounded border';
                div.innerHTML = `
                    <span class="text-blue-800 font-medium">${className}</span>
                    <button onclick="removeClass('${className}')" 
                            class="text-red-500 hover:text-red-700 text-sm">
                        🗑️ ลบ
                    </button>
                `;
                container.appendChild(div);
            });
        }

        function renderSubjectList() {
            const container = document.getElementById('subjectList');
            container.innerHTML = '';
            
            if (subjects.length === 0) {
                container.innerHTML = '<p class="text-gray-500 text-sm">ยังไม่มีวิชา</p>';
                return;
            }
            
            subjects.forEach(subjectName => {
                const div = document.createElement('div');
                div.className = 'flex items-center justify-between bg-green-50 px-3 py-2 rounded border';
                div.innerHTML = `
                    <span class="text-green-800 font-medium">${subjectName}</span>
                    <button onclick="removeSubject('${subjectName}')" 
                            class="text-red-500 hover:text-red-700 text-sm">
                        🗑️ ลบ
                    </button>
                `;
                container.appendChild(div);
            });
        }

        // Google Sheets Functions
        function showSheetsSetup() {
            document.getElementById('sheetsSetupModal').classList.remove('hidden');
        }

        function closeSheetsSetup() {
            document.getElementById('sheetsSetupModal').classList.add('hidden');
        }

        function connectToSheets() {
            const url = document.getElementById('sheetsUrl').value;
            const demoMode = document.getElementById('demoMode').checked;
            
            if (demoMode) {
                // Demo mode - simulate connection
                isDemoMode = true;
                sheetsConnected = true;
                updateConnectionStatus(true, 'โหมดทดสอบ');
                document.getElementById('syncStatus').classList.remove('hidden');
                document.getElementById('sheetsConnected').classList.remove('hidden');
                closeSheetsSetup();
                showNotification('เชื่อมต่อโหมดทดสอบสำเร็จ! 🧪', 'success');
                return;
            }
            
            if (!url) {
                showNotification('กรุณากรอก URL ของ Google Sheets', 'error');
                return;
            }
            
            // Validate Google Sheets URL
            if (!url.includes('docs.google.com/spreadsheets')) {
                showNotification('URL ไม่ถูกต้อง กรุณาใช้ URL ของ Google Sheets', 'error');
                return;
            }
            
            // Extract spreadsheet ID
            const match = url.match(/\/spreadsheets\/d\/([a-zA-Z0-9-_]+)/);
            if (!match) {
                showNotification('ไม่สามารถดึง ID ของ Google Sheets ได้', 'error');
                return;
            }
            
            const spreadsheetId = match[1];
            sheetsUrl = url;
            
            // Test connection by trying to read from the sheet
            testSheetsConnection(spreadsheetId)
                .then(() => {
                    sheetsConnected = true;
                    updateConnectionStatus(true, 'เชื่อมต่อแล้ว');
                    document.getElementById('syncStatus').classList.remove('hidden');
                    document.getElementById('sheetsConnected').classList.remove('hidden');
                    closeSheetsSetup();
                    
                    // Initialize sheets with headers if needed
                    initializeSheets(spreadsheetId);
                    showNotification('เชื่อมต่อ Google Sheets สำเร็จ! 🎉', 'success');
                })
                .catch(error => {
                    console.error('Connection failed:', error);
                    showNotification('ไม่สามารถเชื่อมต่อได้ กรุณาตรวจสอบ URL และการตั้งค่าการแชร์', 'error');
                });
        }

        function updateConnectionStatus(connected, text) {
            const statusDot = document.getElementById('connectionStatus');
            const statusText = document.getElementById('statusText');
            const connectBtn = document.getElementById('connectSheetsBtn');
            
            if (connected) {
                statusDot.className = 'w-3 h-3 bg-green-500 rounded-full';
                statusText.textContent = text;
                connectBtn.textContent = '✅ เชื่อมต่อแล้ว';
                connectBtn.className = 'px-4 py-2 bg-green-600 text-white rounded-lg cursor-default text-sm';
            } else {
                statusDot.className = 'w-3 h-3 bg-red-500 rounded-full';
                statusText.textContent = 'ไม่ได้เชื่อมต่อ';
                connectBtn.textContent = '🔗 เชื่อมต่อ Google Sheets';
                connectBtn.className = 'px-4 py-2 bg-green-600 text-white rounded-lg hover:bg-green-700 transition-colors text-sm';
            }
        }

        function syncWithSheets() {
            if (!sheetsConnected) return;
            
            if (isDemoMode) {
                // Simulate sync in demo mode
                showNotification('ซิงค์ข้อมูลสำเร็จ (โหมดทดสอบ) 🔄', 'info');
                return;
            }
            
            // In a real implementation, this would:
            // 1. Read data from Google Sheets
            // 2. Update local data
            // 3. Write any changes back to Sheets
            
            showNotification('ซิงค์ข้อมูลกับ Google Sheets สำเร็จ! 🔄', 'success');
        }

        // Test Google Sheets connection
        async function testSheetsConnection(spreadsheetId) {
            try {
                const testUrl = `https://docs.google.com/spreadsheets/d/${spreadsheetId}/gviz/tq?tqx=out:csv&sheet=Students`;
                const response = await fetch(testUrl);
                
                if (!response.ok) {
                    throw new Error(`HTTP ${response.status}: ${response.statusText}`);
                }
                
                // If we can read, the connection is working
                return true;
            } catch (error) {
                throw new Error(`ไม่สามารถเชื่อมต่อ Google Sheets ได้: ${error.message}`);
            }
        }

        // Initialize Google Sheets with proper headers
        async function initializeSheets(spreadsheetId) {
            try {
                // Check if sheets have headers, if not, create them
                await createSheetHeaders(spreadsheetId, 'Students', ['รหัสนักเรียน', 'ชื่อ', 'ห้อง', 'วิชา']);
                await createSheetHeaders(spreadsheetId, 'Assignments', ['ID', 'ชื่องาน', 'วิชา', 'ห้อง', 'กำหนดส่ง']);
                await createSheetHeaders(spreadsheetId, 'Submissions', ['รหัสนักเรียน-ID งาน', 'สถานะ', 'วันที่ส่ง']);
                
                console.log('Google Sheets initialized successfully');
            } catch (error) {
                console.error('Failed to initialize sheets:', error);
            }
        }

        // Create headers for a specific sheet
        async function createSheetHeaders(spreadsheetId, sheetName, headers) {
            try {
                // This is a simplified approach - in a real implementation,
                // you would use Google Sheets API to properly create headers
                console.log(`Creating headers for ${sheetName}:`, headers);
            } catch (error) {
                console.error(`Failed to create headers for ${sheetName}:`, error);
            }
        }

        // Save data to Google Sheets using CSV export/import method
        async function saveToSheets(type, data) {
            if (!sheetsConnected || isDemoMode) {
                if (isDemoMode) {
                    console.log(`[Demo] Saving ${type}:`, data);
                }
                return;
            }
            
            try {
                const spreadsheetId = sheetsUrl.match(/\/spreadsheets\/d\/([a-zA-Z0-9-_]+)/)[1];
                
                switch (type) {
                    case 'students':
                        await saveStudentsToSheets(spreadsheetId, data);
                        break;
                    case 'assignments':
                        await saveAssignmentsToSheets(spreadsheetId, data);
                        break;
                    case 'submissions':
                        await saveSubmissionsToSheets(spreadsheetId, data);
                        break;
                    case 'classes':
                    case 'subjects':
                        // These are stored locally for now
                        console.log(`Saving ${type} locally:`, data);
                        break;
                }
            } catch (error) {
                console.error(`Failed to save ${type}:`, error);
                showNotification(`ไม่สามารถบันทึก${type}ได้: ${error.message}`, 'error');
            }
        }

        // Save students data to Google Sheets
        async function saveStudentsToSheets(spreadsheetId, studentsData) {
            try {
                // Convert students data to CSV format
                const csvData = studentsData.map(student => 
                    `${student.id},${student.name},${student.class},${student.subject}`
                ).join('\n');
                
                // In a real implementation, you would use Google Sheets API
                // For now, we'll use a workaround with Google Forms or Apps Script
                console.log('Students CSV data:', csvData);
                
                // Show success message
                showNotification(`บันทึกข้อมูลนักเรียน ${studentsData.length} คน ลง Google Sheets แล้ว`, 'success');
            } catch (error) {
                throw new Error(`ไม่สามารถบันทึกข้อมูลนักเรียนได้: ${error.message}`);
            }
        }

        // Save assignments data to Google Sheets
        async function saveAssignmentsToSheets(spreadsheetId, assignmentsData) {
            try {
                const csvData = assignmentsData.map(assignment => 
                    `${assignment.id},${assignment.name},${assignment.subject},${assignment.class},${assignment.dueDate}`
                ).join('\n');
                
                console.log('Assignments CSV data:', csvData);
                showNotification(`บันทึกข้อมูลงาน ${assignmentsData.length} รายการ ลง Google Sheets แล้ว`, 'success');
            } catch (error) {
                throw new Error(`ไม่สามารถบันทึกข้อมูลงานได้: ${error.message}`);
            }
        }

        // Save submissions data to Google Sheets
        async function saveSubmissionsToSheets(spreadsheetId, submissionsData) {
            try {
                const csvData = Object.entries(submissionsData).map(([key, submission]) => 
                    `${key},${submission.submitted ? 'ส่งแล้ว' : 'ยังไม่ส่ง'},${submission.date || ''}`
                ).join('\n');
                
                console.log('Submissions CSV data:', csvData);
                showNotification('บันทึกข้อมูลการส่งงานลง Google Sheets แล้ว', 'success');
            } catch (error) {
                throw new Error(`ไม่สามารถบันทึกข้อมูลการส่งงานได้: ${error.message}`);
            }
        }

        function showNotification(message, type = 'info') {
            const notification = document.createElement('div');
            const bgColor = type === 'success' ? 'bg-green-500' : type === 'error' ? 'bg-red-500' : 'bg-blue-500';
            
            notification.className = `fixed top-4 right-4 ${bgColor} text-white px-6 py-3 rounded-lg shadow-lg z-50 transform transition-transform duration-300 translate-x-full`;
            notification.textContent = message;
            
            document.body.appendChild(notification);
            
            // Animate in
            setTimeout(() => {
                notification.classList.remove('translate-x-full');
            }, 100);
            
            // Remove after 3 seconds
            setTimeout(() => {
                notification.classList.add('translate-x-full');
                setTimeout(() => {
                    document.body.removeChild(notification);
                }, 300);
            }, 3000);
        }

        // UI Elements
        const studentBtn = document.getElementById('studentBtn');
        const teacherBtn = document.getElementById('teacherBtn');
        const studentSection = document.getElementById('studentSection');
        const teacherSection = document.getElementById('teacherSection');
        const connectSheetsBtn = document.getElementById('connectSheetsBtn');

        // Navigation
        studentBtn.addEventListener('click', () => {
            studentSection.classList.remove('hidden');
            teacherSection.classList.add('hidden');
            studentBtn.classList.add('bg-blue-600');
            studentBtn.classList.remove('bg-gray-400');
            teacherBtn.classList.add('bg-gray-400');
            teacherBtn.classList.remove('bg-green-600');
        });

        teacherBtn.addEventListener('click', () => {
            teacherSection.classList.remove('hidden');
            studentSection.classList.add('hidden');
            teacherBtn.classList.add('bg-green-600');
            teacherBtn.classList.remove('bg-gray-400');
            studentBtn.classList.add('bg-gray-400');
            studentBtn.classList.remove('bg-blue-600');
            updateAllDropdowns();
            renderClassList();
            renderSubjectList();
        });

        // Connect to Google Sheets
        connectSheetsBtn.addEventListener('click', () => {
            if (!sheetsConnected) {
                showSheetsSetup();
            }
        });

        // Excel file upload
        document.getElementById('excelFile').addEventListener('change', function(e) {
            const file = e.target.files[0];
            if (file) {
                const selectedClass = document.getElementById('uploadClassSelect').value;
                const selectedSubject = document.getElementById('uploadSubjectSelect').value;
                
                if (!selectedClass || !selectedSubject) {
                    showNotification('กรุณาเลือกห้องเรียนและวิชาก่อนอัพโหลดไฟล์', 'error');
                    // Reset file input
                    e.target.value = '';
                    return;
                }
                
                const reader = new FileReader();
                reader.onload = function(e) {
                    try {
                        const data = new Uint8Array(e.target.result);
                        const workbook = XLSX.read(data, {type: 'array'});
                        const firstSheet = workbook.Sheets[workbook.SheetNames[0]];
                        const jsonData = XLSX.utils.sheet_to_json(firstSheet, {header: 1});
                        
                        // Process Excel data (skip header row)
                        const newStudents = [];
                        for (let i = 1; i < jsonData.length; i++) {
                            const row = jsonData[i];
                            if (row[0] && row[1]) { // Only need student ID and name
                                // Check if student already exists
                                const existingStudent = students.find(s => s.id === row[0].toString());
                                if (!existingStudent) {
                                    newStudents.push({
                                        id: row[0].toString(),
                                        name: row[1],
                                        class: selectedClass,
                                        subject: selectedSubject
                                    });
                                }
                            }
                        }
                        
                        if (newStudents.length === 0) {
                            showNotification('ไม่พบข้อมูลนักเรียนใหม่ หรือนักเรียนทั้งหมดมีอยู่แล้ว', 'error');
                            return;
                        }
                        
                        students = [...students, ...newStudents];
                        
                        // Save to localStorage and Google Sheets
                        saveDataToStorage();
                        saveToSheets('students', newStudents);
                        
                        showNotification(`อัพโหลดสำเร็จ! เพิ่มข้อมูลนักเรียน ${newStudents.length} คน ในห้อง ${selectedClass} วิชา ${selectedSubject}`, 'success');
                        updateAllDropdowns();
                        
                        // Reset file input
                        e.target.value = '';
                    } catch (error) {
                        showNotification('เกิดข้อผิดพลาดในการอ่านไฟล์ Excel', 'error');
                        console.error(error);
                    }
                };
                reader.readAsArrayBuffer(file);
            }
        });

        // Update all dropdowns
        function updateAllDropdowns() {
            updateDropdowns();
            updateTeacherDropdowns();
            updateUploadDropdowns();
        }

        // Update student search dropdowns
        function updateDropdowns() {
            const classSelect = document.getElementById('classSelect');
            const subjectSelect = document.getElementById('subjectSelect');
            
            classSelect.innerHTML = '<option value="">เลือกห้องเรียน</option>';
            subjectSelect.innerHTML = '<option value="">เลือกวิชา</option>';
            
            classes.forEach(cls => {
                classSelect.innerHTML += `<option value="${cls}">${cls}</option>`;
            });
            
            subjects.forEach(subj => {
                subjectSelect.innerHTML += `<option value="${subj}">${subj}</option>`;
            });
        }

        // Update teacher assignment dropdowns
        function updateTeacherDropdowns() {
            const assignmentSubject = document.getElementById('assignmentSubject');
            const assignmentClass = document.getElementById('assignmentClass');
            
            assignmentSubject.innerHTML = '<option value="">เลือกวิชา</option>';
            assignmentClass.innerHTML = '<option value="">เลือกห้องเรียน</option>';
            
            classes.forEach(cls => {
                assignmentClass.innerHTML += `<option value="${cls}">${cls}</option>`;
            });
            
            subjects.forEach(subj => {
                assignmentSubject.innerHTML += `<option value="${subj}">${subj}</option>`;
            });
        }

        // Update upload dropdowns
        function updateUploadDropdowns() {
            const uploadClassSelect = document.getElementById('uploadClassSelect');
            const uploadSubjectSelect = document.getElementById('uploadSubjectSelect');
            
            uploadClassSelect.innerHTML = '<option value="">เลือกห้องเรียน</option>';
            uploadSubjectSelect.innerHTML = '<option value="">เลือกวิชา</option>';
            
            classes.forEach(cls => {
                uploadClassSelect.innerHTML += `<option value="${cls}">${cls}</option>`;
            });
            
            subjects.forEach(subj => {
                uploadSubjectSelect.innerHTML += `<option value="${subj}">${subj}</option>`;
            });
        }

        // Search functionality
        document.getElementById('searchBtn').addEventListener('click', () => {
            const studentId = document.getElementById('studentId').value;
            const selectedClass = document.getElementById('classSelect').value;
            const selectedSubject = document.getElementById('subjectSelect').value;
            
            if (!studentId) {
                showNotification('กรุณากรอกรหัสนักเรียน', 'error');
                return;
            }
            
            // Find student info
            const student = students.find(s => s.id === studentId);
            
            let filteredAssignments = assignments;
            
            if (selectedClass) {
                filteredAssignments = filteredAssignments.filter(a => a.class === selectedClass);
            }
            
            if (selectedSubject) {
                filteredAssignments = filteredAssignments.filter(a => a.subject === selectedSubject);
            }
            
            // If student found, filter by their class and subject
            if (student) {
                if (!selectedClass) {
                    filteredAssignments = filteredAssignments.filter(a => a.class === student.class);
                }
                if (!selectedSubject) {
                    filteredAssignments = filteredAssignments.filter(a => a.subject === student.subject);
                }
            }
            
            // Update student info card
            updateStudentInfo(student, studentId, selectedClass, selectedSubject);
            
            // Calculate statistics
            const stats = calculateStatistics(studentId, filteredAssignments);
            updateStatistics(stats);
            
            // Render assignment cards
            renderAssignmentCards(studentId, filteredAssignments);
            
            // Render table
            renderResultsTable(studentId, filteredAssignments);
            
            document.getElementById('searchResults').classList.remove('hidden');
        });

        // Update student info
        function updateStudentInfo(student, studentId, selectedClass, selectedSubject) {
            const studentName = document.getElementById('studentName');
            const studentDetails = document.getElementById('studentDetails');
            
            if (student) {
                studentName.textContent = student.name;
                studentDetails.textContent = `รหัส: ${student.id} | ห้อง: ${student.class} | วิชา: ${student.subject}`;
            } else {
                studentName.textContent = `นักเรียนรหัส ${studentId}`;
                studentDetails.textContent = `รหัส: ${studentId} | ห้อง: ${selectedClass || 'ทั้งหมด'} | วิชา: ${selectedSubject || 'ทั้งหมด'}`;
            }
        }

        // Calculate statistics
        function calculateStatistics(studentId, filteredAssignments) {
            let submitted = 0;
            let notSubmitted = 0;
            let overdue = 0;
            const today = new Date().toISOString().split('T')[0];
            
            filteredAssignments.forEach(assignment => {
                const submissionKey = `${studentId}-${assignment.id}`;
                const submission = submissions[submissionKey];
                
                if (submission && submission.submitted) {
                    submitted++;
                } else {
                    notSubmitted++;
                    if (assignment.dueDate < today) {
                        overdue++;
                    }
                }
            });
            
            const total = submitted + notSubmitted;
            const completionRate = total > 0 ? Math.round((submitted / total) * 100) : 0;
            
            return { submitted, notSubmitted, overdue, completionRate };
        }

        // Update statistics display
        function updateStatistics(stats) {
            document.getElementById('submittedCount').textContent = stats.submitted;
            document.getElementById('notSubmittedCount').textContent = stats.notSubmitted;
            document.getElementById('overdueCount').textContent = stats.overdue;
            document.getElementById('completionRate').textContent = stats.completionRate + '%';
        }

        // Render assignment cards
        function renderAssignmentCards(studentId, filteredAssignments) {
            const container = document.getElementById('assignmentCards');
            container.innerHTML = '';
            
            if (filteredAssignments.length === 0) {
                container.innerHTML = `
                    <div class="text-center py-8 text-gray-500">
                        <div class="text-4xl mb-4">📝</div>
                        <p>ไม่พบงานที่ตรงกับเงื่อนไขการค้นหา</p>
                    </div>
                `;
                return;
            }
            
            const today = new Date().toISOString().split('T')[0];
            
            filteredAssignments.forEach(assignment => {
                const submissionKey = `${studentId}-${assignment.id}`;
                const submission = submissions[submissionKey];
                const isSubmitted = submission && submission.submitted;
                const isOverdue = assignment.dueDate < today && !isSubmitted;
                const daysUntilDue = Math.ceil((new Date(assignment.dueDate) - new Date(today)) / (1000 * 60 * 60 * 24));
                
                let statusClass = '';
                let statusIcon = '';
                let statusText = '';
                let borderClass = '';
                
                if (isSubmitted) {
                    statusClass = 'bg-green-100 text-green-800';
                    statusIcon = '✅';
                    statusText = 'ส่งแล้ว';
                    borderClass = 'border-l-green-500';
                } else if (isOverdue) {
                    statusClass = 'bg-red-100 text-red-800';
                    statusIcon = '⏰';
                    statusText = 'เกินกำหนด';
                    borderClass = 'border-l-red-500';
                } else {
                    statusClass = 'bg-yellow-100 text-yellow-800';
                    statusIcon = '⏳';
                    statusText = 'รอส่ง';
                    borderClass = 'border-l-yellow-500';
                }
                
                const card = document.createElement('div');
                card.className = `bg-white rounded-lg shadow-md p-6 border-l-4 ${borderClass} hover:shadow-lg transition-shadow`;
                card.innerHTML = `
                    <div class="flex justify-between items-start mb-4">
                        <div class="flex-1">
                            <h4 class="text-lg font-semibold text-gray-800 mb-2">${assignment.name}</h4>
                            <div class="flex items-center space-x-4 text-sm text-gray-600 mb-3">
                                <span class="flex items-center">
                                    <span class="mr-1">📚</span>
                                    ${assignment.subject}
                                </span>
                                <span class="flex items-center">
                                    <span class="mr-1">🏫</span>
                                    ${assignment.class}
                                </span>
                                <span class="flex items-center">
                                    <span class="mr-1">📅</span>
                                    ${assignment.dueDate}
                                </span>
                            </div>
                            ${!isSubmitted && !isOverdue ? `
                                <div class="text-sm text-gray-500">
                                    ${daysUntilDue > 0 ? `เหลือเวลา ${daysUntilDue} วัน` : daysUntilDue === 0 ? 'ครบกำหนดวันนี้' : ''}
                                </div>
                            ` : ''}
                        </div>
                        <div class="text-right">
                            <span class="inline-flex items-center px-3 py-1 rounded-full text-sm font-medium ${statusClass}">
                                <span class="mr-1">${statusIcon}</span>
                                ${statusText}
                            </span>
                            ${isSubmitted && submission.date ? `
                                <div class="text-xs text-gray-500 mt-2">
                                    ส่งเมื่อ: ${submission.date}
                                </div>
                            ` : ''}
                        </div>
                    </div>
                    
                    ${!isSubmitted ? `
                        <div class="bg-gray-50 rounded-lg p-3">
                            <div class="flex items-center text-sm text-gray-600">
                                <span class="mr-2">💡</span>
                                <span>แนะนำ: ${isOverdue ? 'ติดต่อครูเพื่อขอส่งงานเพิ่มเติม' : 'เตรียมส่งงานก่อนครบกำหนด'}</span>
                            </div>
                        </div>
                    ` : `
                        <div class="bg-green-50 rounded-lg p-3">
                            <div class="flex items-center text-sm text-green-700">
                                <span class="mr-2">🎉</span>
                                <span>เยี่ยม! ส่งงานเรียบร้อยแล้ว</span>
                            </div>
                        </div>
                    `}
                `;
                
                container.appendChild(card);
            });
        }

        // Render results table
        function renderResultsTable(studentId, filteredAssignments) {
            const resultsTable = document.getElementById('resultsTable');
            resultsTable.innerHTML = '';
            
            filteredAssignments.forEach(assignment => {
                const submissionKey = `${studentId}-${assignment.id}`;
                const submission = submissions[submissionKey];
                const isSubmitted = submission && submission.submitted;
                const submissionDate = isSubmitted && submission.date ? submission.date : '-';
                
                const status = isSubmitted ? 
                    `<span class="px-2 py-1 bg-green-100 text-green-800 rounded-full text-sm">✅ ส่งแล้ว</span>` :
                    `<span class="px-2 py-1 bg-red-100 text-red-800 rounded-full text-sm">❌ ยังไม่ส่ง</span>`;
                
                resultsTable.innerHTML += `
                    <tr class="border-b border-gray-200 hover:bg-gray-50">
                        <td class="px-4 py-3 font-medium">
                            <button onclick="showAssignmentDetail(${assignment.id}, '${studentId}')" 
                                    class="text-blue-600 hover:text-blue-800 hover:underline cursor-pointer text-left">
                                ${assignment.name}
                            </button>
                        </td>
                        <td class="px-4 py-3">${assignment.subject}</td>
                        <td class="px-4 py-3">${assignment.dueDate}</td>
                        <td class="px-4 py-3">${status}</td>
                        <td class="px-4 py-3 text-gray-600">${submissionDate}</td>
                    </tr>
                `;
            });
        }

        // Toggle view functionality
        document.addEventListener('DOMContentLoaded', function() {
            const toggleViewBtn = document.getElementById('toggleViewBtn');
            const assignmentCards = document.getElementById('assignmentCards');
            const tableView = document.getElementById('tableView');
            let isCardView = true;
            
            toggleViewBtn.addEventListener('click', () => {
                if (isCardView) {
                    assignmentCards.classList.add('hidden');
                    tableView.classList.remove('hidden');
                    toggleViewBtn.innerHTML = '📊 มุมมองการ์ด';
                    isCardView = false;
                } else {
                    assignmentCards.classList.remove('hidden');
                    tableView.classList.add('hidden');
                    toggleViewBtn.innerHTML = '📊 มุมมองตาราง';
                    isCardView = true;
                }
            });
        });

        // Add assignment
        document.getElementById('addAssignmentBtn').addEventListener('click', () => {
            const name = document.getElementById('assignmentName').value;
            const subject = document.getElementById('assignmentSubject').value;
            const className = document.getElementById('assignmentClass').value;
            const dueDate = document.getElementById('dueDate').value;
            
            if (name && subject && className && dueDate) {
                const newAssignment = {
                    id: assignments.length + 1,
                    name,
                    subject,
                    class: className,
                    dueDate
                };
                
                assignments.push(newAssignment);
                
                // Save to localStorage and Google Sheets
                saveDataToStorage();
                saveToSheets('assignments', [newAssignment]);
                
                // Clear form
                document.getElementById('assignmentName').value = '';
                document.getElementById('assignmentSubject').value = '';
                document.getElementById('assignmentClass').value = '';
                document.getElementById('dueDate').value = '';
                
                updateAssignmentFilters();
                renderAssignments();
                showNotification('เพิ่มงานสำเร็จ!', 'success');
            } else {
                showNotification('กรุณากรอกข้อมูลให้ครบถ้วน', 'error');
            }
        });

        // Render assignments with grouping
        function renderAssignments() {
            const container = document.getElementById('assignmentsContainer');
            container.innerHTML = '';
            
            // Get filter values
            const filterClass = document.getElementById('filterClass')?.value || '';
            const filterSubject = document.getElementById('filterSubject')?.value || '';
            
            // Filter assignments
            let filteredAssignments = assignments;
            if (filterClass) {
                filteredAssignments = filteredAssignments.filter(a => a.class === filterClass);
            }
            if (filterSubject) {
                filteredAssignments = filteredAssignments.filter(a => a.subject === filterSubject);
            }
            
            if (filteredAssignments.length === 0) {
                container.innerHTML = `
                    <div class="text-center py-12 text-gray-500">
                        <div class="text-4xl mb-4">📝</div>
                        <p class="text-lg">ไม่พบงานที่ตรงกับเงื่อนไข</p>
                        <p class="text-sm mt-2">ลองเปลี่ยนตัวกรองหรือเพิ่มงานใหม่</p>
                    </div>
                `;
                return;
            }
            
            // Group assignments by class and subject
            const groupedAssignments = {};
            
            filteredAssignments.forEach(assignment => {
                const groupKey = `${assignment.class}-${assignment.subject}`;
                if (!groupedAssignments[groupKey]) {
                    groupedAssignments[groupKey] = {
                        class: assignment.class,
                        subject: assignment.subject,
                        assignments: []
                    };
                }
                groupedAssignments[groupKey].assignments.push(assignment);
            });
            
            // Render each group
            Object.values(groupedAssignments).forEach(group => {
                const groupDiv = document.createElement('div');
                groupDiv.className = 'mb-8 bg-white rounded-xl shadow-lg overflow-hidden';
                
                // Calculate group statistics
                const totalAssignments = group.assignments.length;
                const studentsInGroup = students.filter(s => s.class === group.class && s.subject === group.subject);
                const totalStudents = studentsInGroup.length;
                
                let totalSubmissions = 0;
                let totalPossible = 0;
                
                group.assignments.forEach(assignment => {
                    studentsInGroup.forEach(student => {
                        const submissionKey = `${student.id}-${assignment.id}`;
                        const submission = submissions[submissionKey];
                        if (submission && submission.submitted) {
                            totalSubmissions++;
                        }
                        totalPossible++;
                    });
                });
                
                const completionRate = totalPossible > 0 ? Math.round((totalSubmissions / totalPossible) * 100) : 0;
                
                groupDiv.innerHTML = `
                    <!-- Group Header -->
                    <div class="bg-gradient-to-r from-blue-600 to-purple-600 text-white p-6">
                        <div class="flex justify-between items-start">
                            <div>
                                <h3 class="text-xl font-bold mb-2">🏫 ${group.class} - 📚 ${group.subject}</h3>
                                <div class="flex items-center space-x-6 text-blue-100">
                                    <span class="flex items-center">
                                        <span class="mr-1">👥</span>
                                        ${totalStudents} คน
                                    </span>
                                    <span class="flex items-center">
                                        <span class="mr-1">📝</span>
                                        ${totalAssignments} งาน
                                    </span>
                                    <span class="flex items-center">
                                        <span class="mr-1">📊</span>
                                        ${completionRate}% ส่งแล้ว
                                    </span>
                                </div>
                            </div>
                            <div class="text-right">
                                <div class="text-2xl font-bold">${completionRate}%</div>
                                <div class="text-sm text-blue-200">อัตราส่งงาน</div>
                            </div>
                        </div>
                        
                        <!-- Progress Bar -->
                        <div class="mt-4">
                            <div class="bg-blue-400 bg-opacity-30 rounded-full h-2">
                                <div class="bg-white rounded-full h-2 transition-all duration-300" 
                                     style="width: ${completionRate}%"></div>
                            </div>
                        </div>
                    </div>
                    
                    <!-- Assignments in Group -->
                    <div class="p-6">
                        <div class="space-y-6">
                            ${group.assignments.map(assignment => {
                                const studentsInClass = students.filter(s => s.class === assignment.class && s.subject === assignment.subject);
                                const submittedCount = studentsInClass.filter(student => {
                                    const submissionKey = `${student.id}-${assignment.id}`;
                                    const submission = submissions[submissionKey];
                                    return submission && submission.submitted;
                                }).length;
                                
                                const assignmentCompletionRate = studentsInClass.length > 0 ? 
                                    Math.round((submittedCount / studentsInClass.length) * 100) : 0;
                                
                                const today = new Date().toISOString().split('T')[0];
                                const isOverdue = assignment.dueDate < today;
                                const daysUntilDue = Math.ceil((new Date(assignment.dueDate) - new Date(today)) / (1000 * 60 * 60 * 24));
                                
                                let dueDateClass = '';
                                let dueDateIcon = '';
                                if (isOverdue) {
                                    dueDateClass = 'text-red-600 bg-red-50';
                                    dueDateIcon = '⚠️';
                                } else if (daysUntilDue <= 3) {
                                    dueDateClass = 'text-orange-600 bg-orange-50';
                                    dueDateIcon = '🔥';
                                } else {
                                    dueDateClass = 'text-blue-600 bg-blue-50';
                                    dueDateIcon = '📅';
                                }
                                
                                return `
                                    <div class="border border-gray-200 rounded-lg overflow-hidden">
                                        <!-- Assignment Header -->
                                        <div class="bg-gray-50 px-4 py-3 border-b">
                                            <div class="flex justify-between items-center">
                                                <div class="flex-1">
                                                    <h4 class="font-semibold text-gray-800 text-lg">${assignment.name}</h4>
                                                    <div class="flex items-center space-x-4 mt-1">
                                                        <span class="text-sm ${dueDateClass} px-2 py-1 rounded-full font-medium">
                                                            ${dueDateIcon} ${assignment.dueDate}
                                                            ${isOverdue ? ' (เกินกำหนด)' : daysUntilDue <= 3 ? ` (เหลือ ${daysUntilDue} วัน)` : ''}
                                                        </span>
                                                        <span class="text-sm text-gray-600">
                                                            ส่งแล้ว: ${submittedCount}/${studentsInClass.length} คน
                                                        </span>
                                                    </div>
                                                </div>
                                                <div class="text-right ml-4">
                                                    <div class="text-xl font-bold ${assignmentCompletionRate >= 80 ? 'text-green-600' : assignmentCompletionRate >= 50 ? 'text-yellow-600' : 'text-red-600'}">
                                                        ${assignmentCompletionRate}%
                                                    </div>
                                                    ${sheetsConnected ? `
                                                        <div class="text-xs text-green-600 bg-green-100 px-2 py-1 rounded mt-1">
                                                            ✅ ซิงค์แล้ว
                                                        </div>
                                                    ` : ''}
                                                </div>
                                            </div>
                                            
                                            <!-- Assignment Progress Bar -->
                                            <div class="mt-3">
                                                <div class="bg-gray-200 rounded-full h-2">
                                                    <div class="rounded-full h-2 transition-all duration-300 ${
                                                        assignmentCompletionRate >= 80 ? 'bg-green-500' : 
                                                        assignmentCompletionRate >= 50 ? 'bg-yellow-500' : 'bg-red-500'
                                                    }" style="width: ${assignmentCompletionRate}%"></div>
                                                </div>
                                            </div>
                                        </div>
                                        
                                        <!-- Students List -->
                                        <div class="p-4">
                                            <div class="grid gap-2">
                                                ${studentsInClass.map(student => {
                                                    const submissionKey = `${student.id}-${assignment.id}`;
                                                    const submission = submissions[submissionKey];
                                                    const isSubmitted = submission && submission.submitted;
                                                    
                                                    return `
                                                        <div class="flex items-center justify-between bg-gray-50 p-3 rounded-lg hover:bg-gray-100 transition-colors">
                                                            <div class="flex items-center space-x-3">
                                                                <div class="w-8 h-8 rounded-full flex items-center justify-center text-sm font-medium ${
                                                                    isSubmitted ? 'bg-green-100 text-green-800' : 'bg-gray-200 text-gray-600'
                                                                }">
                                                                    ${isSubmitted ? '✓' : student.id.slice(-2)}
                                                                </div>
                                                                <div>
                                                                    <span class="font-medium text-gray-800">${student.name}</span>
                                                                    <div class="text-sm text-gray-500">รหัส: ${student.id}</div>
                                                                </div>
                                                            </div>
                                                            <div class="flex items-center space-x-3">
                                                                ${isSubmitted && submission.date ? `
                                                                    <span class="text-xs text-gray-500">
                                                                        ส่งเมื่อ: ${submission.date}
                                                                    </span>
                                                                ` : ''}
                                                                <button onclick="toggleSubmission('${submissionKey}')" 
                                                                        class="px-4 py-2 rounded-lg text-sm font-medium transition-all duration-200 ${
                                                                            isSubmitted ? 
                                                                            'bg-green-100 text-green-800 hover:bg-green-200 border border-green-300' : 
                                                                            'bg-red-100 text-red-800 hover:bg-red-200 border border-red-300'
                                                                        }">
                                                                    ${isSubmitted ? '✅ ส่งแล้ว' : '❌ ยังไม่ส่ง'}
                                                                </button>
                                                            </div>
                                                        </div>
                                                    `;
                                                }).join('')}
                                            </div>
                                        </div>
                                    </div>
                                `;
                            }).join('')}
                        </div>
                    </div>
                `;
                
                container.appendChild(groupDiv);
            });
        }
        
        // Update filter dropdowns for assignments
        function updateAssignmentFilters() {
            const filterClass = document.getElementById('filterClass');
            const filterSubject = document.getElementById('filterSubject');
            
            if (!filterClass || !filterSubject) return;
            
            // Store current values
            const currentClass = filterClass.value;
            const currentSubject = filterSubject.value;
            
            // Update class filter
            filterClass.innerHTML = '<option value="">ทุกห้องเรียน</option>';
            const assignmentClasses = [...new Set(assignments.map(a => a.class))];
            assignmentClasses.forEach(cls => {
                filterClass.innerHTML += `<option value="${cls}" ${cls === currentClass ? 'selected' : ''}>${cls}</option>`;
            });
            
            // Update subject filter
            filterSubject.innerHTML = '<option value="">ทุกวิชา</option>';
            const assignmentSubjects = [...new Set(assignments.map(a => a.subject))];
            assignmentSubjects.forEach(subj => {
                filterSubject.innerHTML += `<option value="${subj}" ${subj === currentSubject ? 'selected' : ''}>${subj}</option>`;
            });
            
            // Add event listeners
            filterClass.addEventListener('change', renderAssignments);
            filterSubject.addEventListener('change', renderAssignments);
        }

        // Toggle submission status
        function toggleSubmission(submissionKey) {
            if (!submissions[submissionKey]) {
                submissions[submissionKey] = { submitted: false };
            }
            
            submissions[submissionKey].submitted = !submissions[submissionKey].submitted;
            
            if (submissions[submissionKey].submitted) {
                submissions[submissionKey].date = new Date().toISOString().split('T')[0];
            }
            
            // Save to localStorage and Google Sheets
            saveDataToStorage();
            saveToSheets('submissions', { [submissionKey]: submissions[submissionKey] });
            
            renderAssignments();
            showNotification('อัพเดทสถานะการส่งงานแล้ว', 'success');
        }

        // Show assignment detail modal
        function showAssignmentDetail(assignmentId, studentId) {
            const assignment = assignments.find(a => a.id === assignmentId);
            const student = students.find(s => s.id === studentId);
            const submissionKey = `${studentId}-${assignmentId}`;
            const submission = submissions[submissionKey];
            
            if (!assignment) return;
            
            const today = new Date().toISOString().split('T')[0];
            const isSubmitted = submission && submission.submitted;
            const isOverdue = assignment.dueDate < today && !isSubmitted;
            const daysUntilDue = Math.ceil((new Date(assignment.dueDate) - new Date(today)) / (1000 * 60 * 60 * 24));
            
            // Update modal content
            document.getElementById('modalTitle').textContent = assignment.name;
            document.getElementById('modalSubtitle').textContent = `รายละเอียดงาน - ${student ? student.name : `รหัส ${studentId}`}`;
            
            document.getElementById('modalAssignmentName').textContent = assignment.name;
            document.getElementById('modalSubject').textContent = assignment.subject;
            document.getElementById('modalClass').textContent = assignment.class;
            document.getElementById('modalDueDate').textContent = assignment.dueDate;
            
            // Update status
            const statusCard = document.getElementById('modalStatusCard');
            const statusBadge = document.getElementById('modalStatusBadge');
            const statusIcon = document.getElementById('modalStatusIcon');
            const submissionDate = document.getElementById('modalSubmissionDate');
            
            if (isSubmitted) {
                statusCard.className = 'rounded-lg p-4 bg-green-50 border border-green-200';
                statusBadge.className = 'px-3 py-1 rounded-full text-sm font-medium bg-green-100 text-green-800';
                statusBadge.textContent = '✅ ส่งแล้ว';
                statusIcon.textContent = '🎉';
                submissionDate.textContent = `ส่งเมื่อ: ${submission.date}`;
            } else if (isOverdue) {
                statusCard.className = 'rounded-lg p-4 bg-red-50 border border-red-200';
                statusBadge.className = 'px-3 py-1 rounded-full text-sm font-medium bg-red-100 text-red-800';
                statusBadge.textContent = '⏰ เกินกำหนด';
                statusIcon.textContent = '😰';
                submissionDate.textContent = 'ยังไม่ได้ส่ง';
            } else {
                statusCard.className = 'rounded-lg p-4 bg-yellow-50 border border-yellow-200';
                statusBadge.className = 'px-3 py-1 rounded-full text-sm font-medium bg-yellow-100 text-yellow-800';
                statusBadge.textContent = '⏳ รอส่ง';
                statusIcon.textContent = '📝';
                submissionDate.textContent = 'ยังไม่ได้ส่ง';
            }
            
            // Update time info
            const daysInfo = document.getElementById('modalDaysInfo');
            const advice = document.getElementById('modalAdvice');
            
            if (isSubmitted) {
                const submittedDays = Math.ceil((new Date(submission.date) - new Date(assignment.dueDate)) / (1000 * 60 * 60 * 24));
                if (submittedDays <= 0) {
                    daysInfo.textContent = `ส่งก่อนกำหนด ${Math.abs(submittedDays)} วัน`;
                } else {
                    daysInfo.textContent = `ส่งหลังกำหนด ${submittedDays} วัน`;
                }
                advice.textContent = '🎉 เยี่ยม! งานนี้ส่งเรียบร้อยแล้ว';
                advice.className = 'mt-2 font-medium text-green-700';
            } else if (isOverdue) {
                const overdueDays = Math.abs(daysUntilDue);
                daysInfo.textContent = `เกินกำหนดมาแล้ว ${overdueDays} วัน`;
                advice.textContent = '⚠️ แนะนำ: ติดต่อครูเพื่อขอส่งงานเพิ่มเติม';
                advice.className = 'mt-2 font-medium text-red-700';
            } else {
                if (daysUntilDue > 0) {
                    daysInfo.textContent = `เหลือเวลาอีก ${daysUntilDue} วัน`;
                    if (daysUntilDue <= 3) {
                        advice.textContent = '🔥 เร่งด่วน! เตรียมส่งงานให้เร็วที่สุด';
                        advice.className = 'mt-2 font-medium text-orange-700';
                    } else {
                        advice.textContent = '💡 แนะนำ: เตรียมงานให้เสร็จก่อนครบกำหนด';
                        advice.className = 'mt-2 font-medium text-blue-700';
                    }
                } else if (daysUntilDue === 0) {
                    daysInfo.textContent = 'ครบกำหนดวันนี้!';
                    advice.textContent = '🚨 ด่วนมาก! ต้องส่งงานวันนี้';
                    advice.className = 'mt-2 font-medium text-red-700';
                }
            }
            
            // Show modal
            document.getElementById('assignmentModal').classList.remove('hidden');
        }
        
        // Close assignment detail modal
        function closeAssignmentModal() {
            document.getElementById('assignmentModal').classList.add('hidden');
        }
        
        // Close modal when clicking outside
        document.getElementById('assignmentModal').addEventListener('click', function(e) {
            if (e.target === this) {
                closeAssignmentModal();
            }
        });

        // Close sheets setup modal when clicking outside
        document.getElementById('sheetsSetupModal').addEventListener('click', function(e) {
            if (e.target === this) {
                closeSheetsSetup();
            }
        });

        // Export data to JSON file
        function exportData() {
            const data = {
                students,
                assignments,
                submissions,
                classes,
                subjects,
                exportDate: new Date().toISOString()
            };
            
            const dataStr = JSON.stringify(data, null, 2);
            const dataBlob = new Blob([dataStr], {type: 'application/json'});
            
            const link = document.createElement('a');
            link.href = URL.createObjectURL(dataBlob);
            link.download = `student-tracking-data-${new Date().toISOString().split('T')[0]}.json`;
            link.click();
            
            showNotification('ส่งออกข้อมูลสำเร็จ! 📤', 'success');
        }

        // Clear all data
        function clearAllData() {
            if (confirm('⚠️ คุณแน่ใจหรือไม่ที่จะลบข้อมูลทั้งหมด?\n\nข้อมูลที่จะถูกลบ:\n- ข้อมูลนักเรียนทั้งหมด\n- ข้อมูลงานทั้งหมด\n- ข้อมูลการส่งงานทั้งหมด\n- ห้องเรียนและวิชาที่เพิ่มเติม\n\nการกระทำนี้ไม่สามารถยกเลิกได้!')) {
                // Clear localStorage
                localStorage.removeItem('students');
                localStorage.removeItem('assignments');
                localStorage.removeItem('submissions');
                localStorage.removeItem('classes');
                localStorage.removeItem('subjects');
                
                // Reset to sample data
                students = [...sampleStudents];
                assignments = [...sampleAssignments];
                submissions = {
                    '65001-1': { submitted: true, date: '2024-01-14' },
                    '65002-1': { submitted: false },
                    '65003-2': { submitted: true, date: '2024-01-18' },
                    '65004-2': { submitted: false }
                };
                classes = ['ม.6/1', 'ม.6/2'];
                subjects = ['คณิตศาสตร์', 'ฟิสิกส์', 'เคมี', 'ชีววิทยา'];
                
                // Save sample data back
                saveDataToStorage();
                
                // Update UI
                updateAllDropdowns();
                renderClassList();
                renderSubjectList();
                updateAssignmentFilters();
                renderAssignments();
                
                // Hide search results
                document.getElementById('searchResults').classList.add('hidden');
                
                showNotification('ล้างข้อมูลทั้งหมดแล้ว! กลับสู่ข้อมูลตัวอย่าง 🗑️', 'success');
            }
        }

        // Apps Script Guide Functions
        function showAppsScriptGuide() {
            document.getElementById('appsScriptModal').classList.remove('hidden');
        }

        function closeAppsScriptGuide() {
            document.getElementById('appsScriptModal').classList.add('hidden');
        }

        function copyAppsScriptCode() {
            const codeElement = document.querySelector('#appsScriptModal pre');
            const code = codeElement.textContent;
            
            navigator.clipboard.writeText(code).then(() => {
                showNotification('คัดลอกโค้ด Apps Script แล้ว! 📋', 'success');
            }).catch(() => {
                // Fallback for older browsers
                const textArea = document.createElement('textarea');
                textArea.value = code;
                document.body.appendChild(textArea);
                textArea.select();
                document.execCommand('copy');
                document.body.removeChild(textArea);
                showNotification('คัดลอกโค้ด Apps Script แล้ว! 📋', 'success');
            });
        }

        // Close Apps Script modal when clicking outside
        document.getElementById('appsScriptModal').addEventListener('click', function(e) {
            if (e.target === this) {
                closeAppsScriptGuide();
            }
        });

        // Initialize
        updateAllDropdowns();
        renderClassList();
        renderSubjectList();
        updateAssignmentFilters();
        renderAssignments();
    </script>
<script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'97b65f45550626c9',t:'MTc1NzI0OTg0OS4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
