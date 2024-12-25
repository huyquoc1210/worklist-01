# Custom Mock Data

Metadata của dịch vụ chỉ chứa mô tả về các thực thể (entities) của dịch vụ. Máy chủ giả (mock server) trong ứng dụng sẽ tự động tạo dữ liệu mẫu ngẫu nhiên dựa trên các kiểu dữ liệu được định nghĩa trong tệp metadata. Để có một môi trường phát triển thực tế hơn, chúng ta sẽ thêm dữ liệu mẫu cụ thể.

file `webapp/localService/metadata.xml` được sử dụng bởi mock server để mô tả dịch vụ OData của chúng ta. Dịch vụ này chỉ có hai thực thể OData, và dữ liệu cho hai thực thể này được lưu trong thư mục `webapp/localService/mockdata`:

1 `Products`:

- Một product có property :
  - `ProductName`,`UnitsInStock` cùng 1 số thuộc ítnh navigation property đến thực thể cung cấp (supplier entity) thông qua `SupplierID`
  - Thực thể này có một property là ID là `ProductID`
  - EntitySet tương ứng là `Products`.

2 `Suppliers`:
Trong phần sau
Các thuộc tính bao gồm CompanyName, Address, City, PostalCode, Country, và các thông tin dạng văn bản khác có kiểu dữ liệu `Edm.String`.

Thực thể này có một thuộc tính ID là `SupplierID`.
EntitySet tương ứng là `Suppliers`.
