# Creating the Initial App

## Home Page (webapp/view/Worklist.view.xml file)

Home page app chỉ hiện thị một table các products bao gồm đơn vị hàng tồn tương ứng. Title table shows. Search filed trong thanh công cụ của tabl cho phép search theo products theo name. Khi nhấn vào hàng của table, ngiwofi dùng sẽ được navigation đến một page tile của products được nhấn

## Data

Bạn có thể chạy ứng dụng với dịch vụ thực hoặc với máy chủ giả (mock server) cung cấp dữ liệu giả (mock data). Trong tệp webapp/localService/mockserver.js, máy chủ giả được cấu hình. Sử dụng máy chủ giả trong hướng dẫn này cho phép chúng ta dễ dàng chạy mã ngay cả khi không có kết nối mạng và không cần máy chủ từ xa để cung cấp dữ liệu cho ứng dụng. Để chạy ứng dụng với máy chủ giả và dữ liệu giả tương ứng, hãy mở tệp `/webapp/test/mockServer.html` trong trình duyệt.

Tệp webapp/localService/metadata.xml được sử dụng bởi máy chủ giả để mô tả dịch vụ OData của chúng ta

## Configuration of the App

webapp/manifest.json

### sap.app

Trong phần này, chúng ta tham chiếu đến tệp `i18n.properties` và sử dụng cú pháp đặc biệt để liên kết các văn bản cho các thuộc tính `title` và `description`. Trong phần dataSour`ces, chúng ta thông báo cho ứng dụng vị trí tìm dịch vụ OData `mainService`. Như bạn có thể đoán, URI tương ứng với `rootUri`của phiên bản máy chủ giả, được định nghĩa trong tệp`webapp/localService/mockserver.js`. Điều quan trọng là hai đường dẫn này phải khớp nhau để máy chủ giả có thể cung cấp dữ liệu kiểm tra mà chúng ta đã định nghĩa ở trên.

### sap.ui5

Trong phần sap.ui5, chúng ta khai báo với tham số `rootView` rằng view `mycompany.myapp.MyWorklistApp.view.App` sẽ được tải và sử dụng làm `rootView` cho ứng dụng của chúng ta.

Sẽ có 2 model là `i18n` và ` ` sẽ tự động liên kết với component: model (i18n) và model ("").Model mặc định tham chiếu đến dữ liệu mainService, được khai báo trong phần `sap.app` là một nguồn dữ liệu OData 2.0. Tệp `i18n` có thể được tìm thấy tại `webapp/i18n/i18n.properties`. Nguồn dữ liệu `mainService` sẽ được máy chủ giả (mock server) của chúng ta giả lập.
