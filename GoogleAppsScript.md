# Hướng Dẫn Kết Nối Form RSVP với Google Sheet

## Bước 1: Tạo Google Sheet

1. Truy cập https://sheets.google.com
2. Tạo sheet mới với tên: "RSVP Thiệp Cưới"
3. Đặt tên các cột ở hàng đầu tiên:
   - A1: STT
   - B1: Họ và Tên
   - C1: Số Điện Thoại
   - D1: Hình Thức Di Chuyển
   - E1: Dự Tiệc Tại
   - F1: Lời Chúc
   - G1: Thời Gian Đăng Ký

## Bước 2: Tạo Google Apps Script

1. Trong Google Sheet, vào menu **Extensions** > **Apps Script**
2. Xóa code mặc định và dán code sau:

```javascript
// Function để xử lý GET request (cần có để tránh lỗi)
function doGet(e) {
  return ContentService.createTextOutput('RSVP Web App is running');
}

// Function chính để xử lý POST request từ form
function doPost(e) {
  try {
    // Lấy active spreadsheet
    const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('RSVP Thiệp Cưới');
    
    // Lấy số dòng hiện tại
    const lastRow = sheet.getLastRow();
    
    // Tạo STT tự động
    const stt = lastRow;
    
    // Lấy dữ liệu từ FormData (e.parameter)
    const hoTen = e.parameter.hoTen || '';
    const soDienThoai = e.parameter.soDienThoai || '';
    const hinhThucDiChuyen = e.parameter.hinhThucDiChuyen || '';
    const duTiecTai = e.parameter.duTiecTai || '';
    const loiChuc = e.parameter.loiChuc || '';
    const thoiGianDangKy = new Date().toLocaleString('vi-VN');
    
    // Thêm dữ liệu vào sheet
    sheet.appendRow([
      stt,
      hoTen,
      soDienThoai,
      hinhThucDiChuyen,
      duTiecTai,
      loiChuc,
      thoiGianDangKy
    ]);
    
    // Trả về response thành công
    return ContentService.createTextOutput(JSON.stringify({
      'result': 'success',
      'message': 'Data saved successfully'
    })).setMimeType(ContentService.MimeType.JSON);
    
  } catch(error) {
    // Log lỗi để debug
    Logger.log('Error: ' + error.toString());
    
    // Trả về response lỗi
    return ContentService.createTextOutput(JSON.stringify({
      'result': 'error',
      'error': error.toString()
    })).setMimeType(ContentService.MimeType.JSON);
  }
}

// Function test để kiểm tra
function testFunction() {
  const testData = {
    hoTen: 'Test User',
    soDienThoai: '0123456789',
    hinhThucDiChuyen: 'Đi xe khách chung của gia đình',
    duTiecTai: 'Nhà Trai (Bắc Ninh)',
    loiChuc: 'Chúc mừng hạnh phúc!',
    thoiGianDangKy: new Date().toLocaleString('vi-VN')
  };
  
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('RSVP Thiệp Cưới');
  const lastRow = sheet.getLastRow();
  
  sheet.appendRow([
    lastRow,
    testData.hoTen,
    testData.soDienThoai,
    testData.hinhThucDiChuyen,
    testData.duTiecTai,
    testData.loiChuc,
    testData.thoiGianDangKy
  ]);
}
```

3. Lưu script với tên: "RSVP Web App"

## Bước 3: Deploy Web App

1. Trong Apps Script editor, click vào nút **Deploy** > **New deployment**
2. Chọn loại deployment: **Web app**
3. Cấu hình:
   - Description: RSVP Web App
   - Execute as: Me (tài khoản của bạn)
   - Who has access: Anyone (cho phép mọi người gửi form)
4. Click **Deploy**
5. **Authorize access** - Chọn tài khoản Google của bạn và cấp quyền truy cập
6. Copy **Web app URL** được hiển thị

## Bước 4: Cập nhật file index.html

1. Mở file `index.html`
2. Tìm dòng:
   ```javascript
   const scriptURL = 'YOUR_WEB_APP_URL_HERE';
   ```
3. Thay `YOUR_WEB_APP_URL_HERE` bằng URL của Web app vừa tạo

## Bước 5: Kiểm tra

1. **Test Apps Script trước:**
   - Trong Apps Script editor, chọn function `testFunction`
   - Click **Run** → Kiểm tra Google Sheet có thêm dữ liệu test không

2. **Test Web App:**
   - Mở file index.html trong trình duyệt
   - Điền form RSVP và submit
   - Kiểm tra Google Sheet để xem dữ liệu đã được thêm vào chưa

## Lưu ý quan trọng:

- **Bảo mật**: URL Web app là public, chỉ nên chia sẻ với người tin cậy
- **Testing**: Test kỹ trước khi gửi thiệp cưới cho khách
- **Backup**: Thường xuyên backup Google Sheet
- **Rate limiting**: Google có giới hạn request, nhưng cho form cưới thì không vấn đề

## Troubleshooting:

### Lỗi "Không tìm thấy hàm doGet"
- **Nguyên nhân**: Apps Script bắt buộc phải có `doGet()` function
- **Solution**: Đảm bảo code có cả `doGet()` và `doPost()`

### Lỗi "Permission denied"
- **Nguyên nhân**: Chưa authorize hoặc deploy sai
- **Solution**: Redeploy Web app với đúng permissions

### Lỗi "Cannot read property"
- **Nguyên nhân**: Dữ liệu gửi đi không đúng format
- **Solution**: Kiểm tra lại JavaScript code

### Lỗi "Sheet not found"
- **Nguyên nhân**: Tên sheet không đúng
- **Solution**: Đảm bảo sheet tên đúng "RSVP Thiệp Cưới"

Sau khi hoàn thành, mọi người submit form sẽ được lưu tự động vào Google Sheet của bạn!
