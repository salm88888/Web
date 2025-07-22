<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8" />
  <title>نتائج الطلاب</title>
  <style>
    body {
      font-family: 'Cairo', sans-serif;
      background-color: #f0f8f8;
      text-align: center;
      padding: 2rem;
    }
    input {
      padding: 0.5rem;
      width: 300px;
      font-size: 1rem;
      text-align: center;
    }
    table {
      margin: 2rem auto;
      border-collapse: collapse;
      width: 90%;
    }
    th, td {
      border: 1px solid #ccc;
      padding: 0.7rem;
    }
    th {
      background-color: #279c9c;
      color: white;
    }
    .not-found {
      margin-top: 2rem;
      color: red;
      font-weight: bold;
    }
  </style>
</head>
<body>

  <h2>🔍 ابحث عن نتيجة الطالب</h2>
  <input type="text" id="searchInput" placeholder="أدخل اسم الطالب" oninput="searchStudent()" />

  <div id="result"></div>

  <script>
    let students = [];

    // تنظيف الاسم (إزالة المسافات الزائدة، تحويل لحروف صغيرة)
    function cleanName(name) {
      return name.replace(/\s+/g, ' ').trim().toLowerCase();
    }

    // تحميل البيانات من ملف CSV
    fetch("https://docs.google.com/spreadsheets/d/e/2PACX-1vRxaS4EWh3wO-s7YJ1tGeZZpUw4AeSPELfYRbmY9zTpmlJ1qOOTaGbT_mtvl_rTyGVnSGcYFtj8yaDR/pub?gid=1484461829&single=true&output=csv")
      .then(res => res.text())
      .then(csv => {
        const lines = csv.trim().split('\n');
        const headers = lines[0].split(',');
        students = lines.slice(1).map(line => {
          const values = line.split(',');
          const entry = {};
          headers.forEach((h, i) => {
            entry[h.trim()] = values[i]?.trim();
          });
          return entry;
        });
      });

    function searchStudent() {
      const input = cleanName(document.getElementById("searchInput").value);
      const resultDiv = document.getElementById("result");
      if (!input) {
        resultDiv.innerHTML = "";
        return;
      }

      const matched = students.filter(s => cleanName(s["الاسم"]).includes(input));

      if (matched.length === 0) {
        resultDiv.innerHTML = "<div class='not-found'>لم يتم العثور على نتائج</div>";
        return;
      }

      // بناء الجدول
      let html = "<table><thead><tr>";
      Object.keys(matched[0]).forEach(key => {
        html += `<th>${key}</th>`;
      });
      html += "</tr></thead><tbody>";

      matched.forEach(row => {
        html += "<tr>";
        Object.values(row).forEach(value => {
          html += `<td>${value}</td>`;
        });
        html += "</tr>";
      });

      html += "</tbody></table>";
      resultDiv.innerHTML = html;
    }
  </script>
</body>
</html>
