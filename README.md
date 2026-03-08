# passport-ocr-project
Used to test the system
<!DOCTYPE html>
<html lang="th">
<head>
  <meta charset="UTF-8">
  <title>OCR ตรวจสอบพาสปอร์ต</title>
  <link rel="stylesheet" href="style.css">
  <script src="https://cdn.jsdelivr.net/npm/tesseract.js@2/dist/tesseract.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/xlsx/dist/xlsx.full.min.js"></script>
</head>
<body>
  <h1>ระบบ OCR ตรวจสอบพาสปอร์ต + Export Excel</h1>

  <!-- อัปโหลดรูปภาพ -->
  <section class="box">
    <h2>อัปโหลดรูปภาพพาสปอร์ต</h2>
    <input type="file" id="passportImage" accept="image/*">
    <button onclick="runOCR()">อ่านข้อมูลจากรูปภาพ</button>
    <pre id="ocrResult">ข้อความที่ OCR อ่านได้จากรูปภาพ</pre>
  </section>

  <!-- ฟอร์มกรอกข้อมูล -->
  <section class="box">
    <h2>กรอกรายละเอียดพาสปอร์ต</h2>
    <input type="text" id="name" placeholder="Name">
    <input type="text" id="surname" placeholder="Surname">
    <input type="text" id="thaiName" placeholder="ชื่อ">
    <input type="text" id="thaiSurname" placeholder="นามสกุล">
    <input type="text" id="sex" placeholder="เพศ (Sex)">
    <input type="text" id="countryCode" placeholder="ประเทศ (Country code)">
    <input type="date" id="dob">
    <input type="text" id="passportNumber" placeholder="เลขพาสปอร์ต">
    <input type="date" id="expiryDate">
    <input type="date" id="issueDate">
    <input type="text" id="placeOfBirth" placeholder="สถานที่เกิด">
    <button onclick="addTraveler()">เพิ่มข้อมูลนักท่องเที่ยว</button>
    <button onclick="exportExcel()">Export to Excel</button>
  </section>

  <!-- ตารางสรุป -->
  <section class="box">
    <h2>ตารางสรุปผล</h2>
    <table border="1" id="summaryTable">
      <tr>
        <th>Name</th>
        <th>Surname</th>
        <th>ชื่อ</th>
        <th>นามสกุล</th>
        <th>Sex</th>
        <th>Country code</th>
        <th>Date of Birth</th>
        <th>Passport Number</th>
        <th>Expiry Date</th>
        <th>Date of Issue</th>
        <th>Place of Birth</th>
      </tr>
    </table>
  </section>

  <script src="script.js"></script>
</body>
</html>
body {
  font-family: Arial, sans-serif;
  margin: 40px;
  background: #f9f9f9;
}
h1 {
  text-align: center;
  color: #2c3e50;
}
.box {
  background: #fff;
  padding: 20px;
  margin-bottom: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 6px rgba(0,0,0,0.1);
}
input, button {
  display: block;
  margin-top: 10px;
  padding: 8px;
  width: 100%;
  max-width: 400px;
}
button {
  background: #27ae60;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
button:hover {
  background: #219150;
}
pre {
  background: #eee;
  padding: 10px;
  white-space: pre-wrap;
}
let travelers = [];
let ocrData = {};

function runOCR() {
  const image = document.getElementById("passportImage").files[0];
  if (!image) {
    alert("กรุณาเลือกไฟล์รูปภาพก่อน");
    return;
  }
  Tesseract.recognize(image, 'eng')
    .then(({ data: { text } }) => {
      document.getElementById("ocrResult").innerText = text;

      // ตัวอย่างข้อมูลที่ OCR อาจดึงได้ (สมมติ)
      ocrData = {
        name: "JOHN",
        surname: "DOE",
        thaiName: "จอห์น",
        thaiSurname: "โด",
        sex: "M",
        countryCode: "THA",
        dob: "1990-01-01",
        passportNumber: "AB1234567",
        expiryDate: "2028-12-31",
        issueDate: "2018-01-01",
        placeOfBirth: "BANGKOK"
      };
    });
}

function addTraveler() {
  const traveler = {
    name: document.getElementById("name").value,
    surname: document.getElementById("surname").value,
    thaiName: document.getElementById("thaiName").value,
    thaiSurname: document.getElementById("thaiSurname").value,
    sex: document.getElementById("sex").value,
    countryCode: document.getElementById("countryCode").value,
    dob: document.getElementById("dob").value,
    passportNumber: document.getElementById("passportNumber").value,
    expiryDate: document.getElementById("expiryDate").value,
    issueDate: document.getElementById("issueDate").value,
    placeOfBirth: document.getElementById("placeOfBirth").value
  };
  travelers.push(traveler);

  const table = document.getElementById("summaryTable");
  const row = table.insertRow();
  Object.values(traveler).forEach(val => {
    const cell = row.insertCell();
    cell.innerText = val;
  });
}

function exportExcel() {
  const ws = XLSX.utils.json_to_sheet(travelers);
  const wb = XLSX.utils.book_new();
  XLSX.utils.book_append_sheet(wb, ws, "PassportData");
  XLSX.writeFile(wb, "passport_data.xlsx");
}
