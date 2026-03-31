# Checklist Debug Google Sheet Integration

## 1. Kiểm tra Google Sheet
- [ ] Sheet có tên đúng "RSVP Thiệp Cưới"?
- [ ] Hàng đầu tiên có đúng tên cột?
  - A1: STT
  - B1: Họ và Tên
  - C1: Số Điện Thoại
  - D1: Hình Thức Di Chuyển
  - E1: Dự Tiệc Tại
  - F1: Lời Chúc
  - G1: Thời Gian Đăng Ký

## 2. Kiểm tra Apps Script
- [ ] Code đã paste đúng?
- [ ] Đã save script?
- [ ] Đã deploy thành công?
- [ ] Web app URL có dạng: https://script.google.com/macros/s/.../exec?

## 3. Kiểm tra Permissions
- [ ] Đã authorize access cho Google Apps Script?
- [ ] Deployment setting: "Anyone" có access?

## 4. Test Steps
1. Mở Developer Tools (F12)
2. Vào tab Console
3. Điền form và submit
4. Xem có error message nào không

## 5. Common Errors & Solutions

### Error: "CORS policy"
**Solution:** Thay `mode: 'no-cors'` thành `mode: 'cors'` trong fetch

### Error: "Permission denied"
**Solution:** Redeploy Web app với đúng permissions

### Error: "Cannot read property"
**Solution:** Kiểm tra lại selectors trong JavaScript

### Error: "Script function not found"
**Solution:** Đảm bảo function name là `doPost`

## 6. Alternative Solution

Nếu vẫn không được, thử code này:

```javascript
function sendToGoogleSheet(data) {
    const scriptURL = 'https://script.google.com/macros/s/AKfycbzfh989YXMTpiw19r7HxQwcwOIIZ-Zi2QtdIx4bh6hHUs0dA_sh8azo_OL_oCgQXMpZ/exec';
    
    // Tạo form data thay vì JSON
    const formData = new FormData();
    formData.append('hoTen', data.hoTen);
    formData.append('soDienThoai', data.soDienThoai);
    formData.append('hinhThucDiChuyen', data.hinhThucDiChuyen);
    formData.append('duTiecTai', data.duTiecTai);
    formData.append('loiChuc', data.loiChuc);
    formData.append('thoiGianDangKy', data.thoiGianDangKy);
    
    fetch(scriptURL, {
        method: 'POST',
        body: formData
    })
    .then(response => response.text())
    .then(result => {
        console.log('Success:', result);
        alert('Dữ liệu đã được lưu thành công!');
    })
    .catch(error => {
        console.error('Error:', error);
        alert('Có lỗi xảy ra, vui lòng thử lại!');
    });
}
```

Và update Apps Script:

```javascript
function doPost(e) {
  try {
    const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('RSVP Thiệp Cưới');
    const lastRow = sheet.getLastRow();
    
    sheet.appendRow([
      lastRow,
      e.parameter.hoTen,
      e.parameter.soDienThoai,
      e.parameter.hinhThucDiChuyen,
      e.parameter.duTiecTai,
      e.parameter.loiChuc,
      e.parameter.thoiGianDangKy
    ]);
    
    return ContentService.createTextOutput('Success');
  } catch(error) {
    return ContentService.createTextOutput('Error: ' + error.toString());
  }
}
```

## 7. Quick Test
Test với URL này trong browser:
https://script.google.com/macros/s/AKfycbzfh989YXMTpiw19r7HxQwcwOIIZ-Zi2QtdIx4bh6hHUs0dA_sh8azo_OL_oCgQXMpZ/exec

Nếu thấy "Error: Cannot read property" thì cần check lại Apps Script.
