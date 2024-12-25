# Extending the Worklist Table

Chúng ta sẽ chỉnh bảng worklist để thêm các cột bổ sung cho kịc bản quản lý kho sản phẩm.Chúng hiển thị `product price`, `number of units`, `supplier`.

Chúng ta display company name của supplier, trong mỗi cột riêng bịtee trong bảng cho mỗi products. Vì chúng ta mở rộng tập hợp items của bảng với tham số `expand` cho thực thể `Supplier`. Với điều này, dữ liệu của supplier sẽ được bao gồm ngay trong yêu cầu dịch vụ cho các sản phẩm.

Mở rộng supplier(nhà cung cấp). Hơn nữa, việc cho phép chúng ta liên kết trực tiếp `{Supplier/CompanyName}` sau này

> OData $expand rất hữu ích khi kết hợp dữ liệu từ các thực thể dịch vụ khác nhau. Thay vì phải gửi một yêu cầu dịch vụ bổ sung cho thực thể thứ hai, chúng ta chỉ cần mở rộng yêu cầu dịch vụ để bao gồm thực thể thứ hai – giống như một phép kết hợp (join) trong cơ sở dữ liệu quan hệ.

> file metadata của service `webapp/localService/metadata.xml`. Trong metadata, bạn sẽ thấy một danh sách các thực thể có sẵn trong dịch vụ này, ví dụ như `Products` và `Suppliers`.

Trong thực thể Products, có mối quan hệ với `Supplier` được định nghĩa dưới một `NavigationProperty`. Thuộc tính điều hướng (navigation property) này liên kết hai thực thể trong một dịch vụ OData và trong trường hợp này, nó gán nhà cung cấp (`supplier`) cho sản phẩm (`product`).

Khi sử dụng một dịch vụ OData, giao diện của nó có thể được xem trực tiếp trong trình duyệt bằng cách truy cập URL của dịch vụ (ví dụ:`http://services.odata.org/V3/Northwind/Northwind.svc/$metadata` cho dịch vụ OData thử nghiệm Northwind). Trong dự án ứng dụng của chúng ta, thay vào đó, chúng ta sử dụng dữ liệu giả lập (mock data) cục bộ và cung cấp dữ liệu này thông qua máy chủ giả lập (mock server).

Tiếp theo, chúng ta đổi định nghĩa của table. Chúng ta sẽ định nghĩa các cột mới và update hiện thuộc tính
`columns aggregation`

`Chi tiết các cột`:
`Product Name`
Tên sản phẩm là cột đầu tiên và luôn hiển thị trên mọi thiết bị.
`Supplier`
Mỗi sản phẩm có một nhà cung cấp.
Cột này chứa tên công ty của nhà cung cấp cung cấp sản phẩm đó.
Trên các thiết bị màn hình nhỏ như điện thoại thông minh, cột này sẽ bị ẩn vì không gian màn hình cho bảng là hạn chế.
`Price`
Đơn vị tiền tệ của giá sản phẩm là Euro (EUR).
Trong ứng dụng này, chúng ta quan tâm đến mức tồn kho, vì vậy số lượng sản phẩm quan trọng hơn giá của chúng.
Giá vẫn hữu ích nên không bị loại bỏ hoàn toàn. Tuy nhiên, trường này không quan trọng bằng các trường về đơn vị và sẽ được hiển thị dưới dạng popin trên các thiết bị màn hình nhỏ.
`Units on Order`
Cột này hiển thị số lượng sản phẩm đã được đặt hàng nhưng chưa được nhận.
Nói cách khác, đây là số lượng hàng đã đặt nhưng chưa có trong kho.
Tình trạng thiếu hàng có thể dễ dàng được giải quyết bằng cách đặt hàng lại trước. (Chức năng này sẽ được thêm vào sau).
Trường này sẽ hiển thị dưới dạng popin trên thiết bị điện thoại thông minh.
`Units in Stock`
Cột này chứa số lượng sản phẩm hiện có trong kho để bán.
Đây là cột quan trọng nhất trong ứng dụng quản lý tồn kho sản phẩm.
Vì vậy, cột này hiển thị trên mọi thiết bị và không hiển thị dưới dạng popin.
Sau này, chúng ta sẽ sử dụng cột này để hiển thị trạng thái kho của các sản phẩm nhằm thu hút sự chú ý đến bất kỳ vấn đề nào liên quan đến kho của các sản phẩm.

Để trực quan hóa các số liệu về số lượng tồn kho cho mỗi sản phẩm trong bảng, chúng ta sẽ sử dụng `ValueState` tùy thuộc vào số lượng sản phẩm còn lại. Điều này sẽ giúp người dùng dễ dàng nhận thấy các vấn đề quan trọng, chẳng hạn như tình trạng thiếu hàng. Chúng ta sẽ sử dụng một `formatter` để áp dụng logic này.

Các bước thực hiện:
Mở rộng formatter:
Thêm một hàm formatter mới tên là quantityState vào tệp `webapp/model/formatter.js.`

Tải `ValueState`:
Đảm bảo rằng loại `ValueState` được tải làm một phụ thuộc bổ sung. `ValueState` sẽ giúp xác định trạng thái của các ô (ví dụ: Error, Warning, Success).

Cài đặt logic `formatter`:
`Formatter` sẽ kiểm tra số lượng tồn kho và trả về một trạng thái ValueState khác nhau tùy vào số lượng sản phẩm còn lại:

- Hết hàng (0 sản phẩm):
  Trả về trạng thái Error. Văn bản trong ô Units in Stock sẽ có màu đỏ để cảnh báo người dùng về tình trạng hết hàng.

- Tồn kho rất thấp (10 sản phẩm trở xuống):
  Trả về trạng thái Warning. Văn bản sẽ có màu cam để cảnh báo người dùng rằng số lượng sản phẩm còn lại là rất ít.

- Tồn kho đủ (hơn 10 sản phẩm):
  Trả về trạng thái Success. Văn bản sẽ có màu xanh lá cây để chỉ ra rằng số lượng sản phẩm còn lại là đủ.

1. Product Name

- Hiển thị `ProductName`
- Sử dụng `sap.m.ObjectIdentifier` để hiển thị giá trị này

```xml
<ObjectIdentifier text="{ProductName}" />
```

2. Supplier

- Sử dụng `sap.m.Text` để hiển thị Supplier/CompanyName
- Property `text` liên kết `Supplier/CompanyName`.Điều này tham chiếu đến thuộc tính `CompanyName` trong `NavigationProperty` của `Supplier`

```xml
 <Text text="{Supplier/CompanyName}" />
```

3. Price

- Sử dụng `sap.m.ObjectNumber` để hiển thị giá
- Sử dụng `custom formatter` để định dạng giá, `webapp/model/formatter.js`
- Property `unit` không liên kết dữ liệu được gán `EUR`, vì đơn vị tiền tệ không có trong model của app.

```xml
<ObjectNumber number="{Price}" unit="EUR" formatter="model.formatter.priceFormatter" />
```

4. Units on Order

- Sử dụng `sap.m.ObjectNumber` để hiện thị số lượng đơn vị đã đặt hàng
- Thuộc tính `unit` được gán cố định là “PC” (viết tắt của "pieces").

```xml
<ObjectNumber number="{UnitsOnOrder}" unit="PC" />
```

5. Units in Stock

- Binding: Bạn đang sử dụng path: 'UnitsInStock' để liên kết dữ liệu và đang gọi đến formatter.quantityState.
- Formatter: Cần đảm bảo rằng formatter quantityState được định nghĩa và áp dụng đúng trong tệp formatter.js.
