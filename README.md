# Danh sách function có sẵn
#### 1. hàm trả về 1 url dựa trên name (nếu sử dụng trong admin nó sẽ thêm tiền tố admin/ phía trước)
```php
function url(?string $name = null, $parameters = null, ?array $getParams = null): string{}
# Parameters:
string| null $name, 
array|null $parameters 
array|null $getParams
# returns string URL
```

#### 2. hàm chuyển hướng đến 1 url có sẵn
```php
function redirect(string $url, ?int $code = null): void{}
# Parameters:
string $url
int | null $code
```
#### 3. tạo token
```php
csrf_token();
# returns string|null
```
#### 4. Session
function session() đại diện cho class NINA\Core\Session\Session::class;
```php

Cách sử dụng:
session()->get($key) // lấy ra 1 session có key là $key
session()->set($key,$value) //tạo 1 session $key:$value
session()->getID() //lấy session_id();
session()->isset($key) //kiểm tra 1 session có tồn tại không?
session()->put($key, $value = null)
session()->has($key)
session()->unset($key) //xóa 1 session
session()->storage() //trả về danh sách các session
```

#### 5. assets()
Hàm assets tạo URL cho nội dung bằng cách sử dụng sơ đồ yêu cầu hiện tại (HTTP hoặc HTTPS)
```php
function assets($path=''){
    return request()->root().'/'.$path;
}
# Parameters:
string | null $path
# VD:
ASSET_URL=http://example.com
$url = asset('/img/photo.jpg'); // http://example.com/img/photo.jpg
```
#### 6. assets_photo()
```php
function assets_photo($path,$size,$photo,$type=''){}
# Parameters:
string $path // thư mục hình ảnh (photo, product, news...)
string $size // kích thước cần cắt thumb
string $photo // tên file ảnh
string|null $type ('thumbs' || 'watermarks') // nếu $type = '', hàm sẽ trả về đường dẫn ảnh.
```

#### 7. các function khác
```php
thumb_path() //trả về đường dẫn đến thư mục thumb
cache_path() //trả về đường dẫn đến thư mục cache
__(string $key) // trả về biến ngôn ngữ định nghĩa trong thư mục LANG. VD: __('home') => Trang chủ
function view($view = '',  $data = []){} // trả về html trong template truyền vào. VD: view('index.home',[]);
agent() // trả về 1 singleton agent, được dùng để truy cập các phương thức trong Class Agent.
seo() // trả về 1 singleton seo, được dùng để truy cập các phương thức trong Class Seo.
cssminify() // trả về 1 singleton cssminify, được dùng để truy cập các phương thức trong Class CssMinify.
jsminify() // trả về 1 singleton jsminify, được dùng để truy cập các phương thức trong Class JsMinify.
```
# Các method hay sử dụng liên quan đến request
```php
request() // trả về đối tượng Illuminate\Http\Request. VD: dd(request())
```
```php
request()->url() // Lấy URL hiện tại (không có chuỗi truy vấn) cho yêu cầu.
```
```php
request()->input($key = null,$default = null ){}
# Truy xuất một mục đầu vào từ yêu cầu
# Parameters:
null|string $key, mixed $default
#Returns: 
mixed 
```
```php
request()->query($key = null,$default = null){}
# lấy các giá trị query trên URL
# Parameters:
null|string $key, array|null|string $default
# Returns:
array|null|string 
```
```php
request()->getHost() // trả về tên miền hiện tại (không bao gồm giao thức)
```
```php
request()->has($key) // Kiểm tra 1 biến có trên URL không, ( hoạt động với cả POST)
```

# Các truy vấn CSDL
#### Truy xuất một hàng/cột từ một bảng
```php
$user = DB::table('users')->where('name', 'John')->first();
return $user->email;

Lấy giá trị có điều kiện:
$email = DB::table('users')->where('name', 'John')->value('email');

Lấy giá trị theo ID:
$user = DB::table('users')->find(3);
```
#### Aggregates
Sử dụng count, max, min, avg, sum
```php
$users = DB::table('users')->count();
 
$price = DB::table('orders')->max('price');

$price = DB::table('orders')->where('finalized', 1)->avg('price');
```
#### Raw Expressions
```php
$users = DB::table('users')
             ->select(DB::raw('count(*) as user_count, status'))
             ->where('status', '<>', 1)
             ->groupBy('status')
             ->get();
```
#### Raw Methods
```php
#selectRaw
$orders = DB::table('orders') ->selectRaw('price * ? as price_with_tax', [1.0825]) ->get();

#whereRaw / orWhereRaw
$orders = DB::table('orders')->whereRaw('price > IF(state = "TX", ?, 100)', [200])->get();
```
#### Điều kiện Where cơ bản [Xem thêm tại](https://laravel.com/docs/11.x/queries#basic-where-clauses)
#### Điều kiện where OrWhere [Xem thêm tại](https://laravel.com/docs/11.x/queries#or-where-clauses)
=======================

## Thanh Toán Online
```php
Copy thư mục NINAGateway bỏ vào /src .
Khai báo provider và aliase trong file config/app.php
'aliases' => Facade::defaultAliases()->merge([
    ....
    "Gateway" =>  \NINA\NINAGateway\Facade\Gateway::class,
])->toArray(),
'providers' => ServiceProvider::defaultProviders()->merge([
   ...
    \NINA\NINAGateway\Providers\GatewayServiceProvider::class
])->toArray(),

use NINA\NINAGateway\Facade\Gateway; // sử dụng Gateway
```
### VNPay

#### Tạo yêu cầu thanh toán:

```php

$response =  Gateway::purchase([
    'vnp_TxnRef' => time(),
    'vnp_OrderType' => 100000,
    'vnp_OrderInfo' => time(),
    'vnp_IpAddr' => '127.0.0.1',
    'vnp_Amount' => 1000000,
    'vnp_ReturnUrl' => 'https://nina.vn/',
])->send();

if ($response->isRedirect()) {
    $redirectUrl = $response->getRedirectUrl(); // URL trang thanh toán
    // chuyển hướng sang trang VNPay để thanh toán
}
```

Kham khảo thêm các tham trị khi tạo yêu cầu và VNPay trả về tại [đây](https://sandbox.vnpayment.vn/apis/docs/huong-dan-tich-hop/#t%E1%BA%A1o-url-thanh-to%C3%A1n).

#### Kiểm tra thông tin `vnp_ReturnUrl` khi khách được VNPay redirect về:

```php
$response = Gateway::completePurchase()->send();

if ($response->isSuccessful()) {
    // xử lý kết quả và hiển thị.
    echo $response->vnp_Amount;
    echo $response->vnp_TxnRef;
    
    var_dump($response->getData()); // toàn bộ data do VNPay gửi sang.
    
} else {

    echo $response->getMessage();
}
```

Kham khảo thêm các tham trị khi VNPay trả về tại [đây](https://sandbox.vnpayment.vn/apis/docs/huong-dan-tich-hop/#code-returnurl).

#### Kiểm tra thông tin `IPN` do VNPay gửi sang:

```php
$response = Gateway::notification()->send();

if ($response->isSuccessful()) {
    // xử lý kết quả.
    echo $response->vnp_Amount;
    echo $response->vnp_TxnRef;
    
    var_dump($response->getData()); // toàn bộ data do VNPay gửi sang.
    
} else {

    echo $response->getMessage();
}
```

Kham khảo thêm các tham trị khi VNPay gửi sang tại [đây](https://sandbox.vnpayment.vn/apis/docs/huong-dan-tich-hop/#code-ipn-url).

#### Kiểm tra trạng thái giao dịch:

```php
$response = Gateway::queryTransaction([
   'vnp_TxnRef' => 1717649685,
    'vnp_OrderInfo' => 1717649685,
    'vnp_IpAddr' => '127.0.0.1',
    'vnp_TransactionDate' => 20240606115523,
    'vnp_TransactionNo' => 14446261,
])->send();

if ($response->isSuccessful()) {
    // xử lý kết quả và hiển thị.
    echo $response->getTransactionId();
    echo $response->getTransactionReference();
    
    var_dump($response->getData()); // toàn bộ data do VNPay gửi về.
    
} else {

    echo $response->getMessage();
}
```
Kham khảo thêm các tham trị khi tạo yêu cầu và VNPay trả về tại [đây](https://goo.gl/FHdM5B).

### OnePay
#### Nội địa
##### Khởi tạo gateway:

```php
use NINA\NINAGateway\Facade\Gateway;

$onePay = Gateway::gateway('\NINA\NINAGateway\OnePay\DomesticGateway');
$onePay->initialize(config('gateways.gateways.OnePayDomestic.options'));

```
Gateway khởi tạo ở trên dùng để tạo các yêu cầu xử lý đến OnePay hoặc dùng để nhận yêu cầu do OnePay gửi đến.

##### Tạo yêu cầu thanh toán:

```php
$response = $onePay->purchase([
    'AgainLink' => 'https://ninaphp.info/',
    'vpc_MerchTxnRef' => time(),
    'vpc_ReturnURL' => 'https://ninaphp.info/',
    'vpc_TicketNo' => '127.0.0.1',
    'vpc_Amount' => '200000',
    'vpc_OrderInfo' => time(),
])->send();

if ($response->isRedirect()) {
    $redirectUrl = $response->getRedirectUrl();
    // chuyển khách sang trang OnePay để thanh toán
}
```

Kham khảo thêm các tham trị khi tạo yêu cầu và OnePay trả về tại [đây](https://mtf.onepay.vn/developer/resource/documents/docx/quy_trinh_tich_hop-noidia.pdf).

##### Kiểm tra thông tin `vpc_ReturnURL` khi khách được OnePay redirect về:

```php
$response = $onePay->completePurchase()->send();

if ($response->isSuccessful()) {
    // xử lý kết quả và hiển thị.
    echo $response->vpc_Amount;
    echo $response->vpc_MerchTxnRef;
    
    var_dump($response->getData()); // toàn bộ data do OnePay gửi sang.
    
} else {

    echo $response->getMessage();
}
```

Kham khảo thêm các tham trị khi OnePay trả về tại [đây](https://mtf.onepay.vn/developer/resource/documents/docx/quy_trinh_tich_hop-noidia.pdf).

##### Kiểm tra thông tin `IPN` do OnePay gửi sang:

```php
$response = $onePay->notification()->send();

if ($response->isSuccessful()) {
    //xử lý kết quả và hiển thị.
    echo $response->vpc_Amount;
    echo $response->vpc_MerchTxnRef;
    
    var_dump($response->getData()); // toàn bộ data do OnePay gửi sang.
    
} else {

    echo $response->getMessage();
}
```

Kham khảo thêm các tham trị khi OnePay gửi sang tại [đây](https://mtf.onepay.vn/developer/resource/documents/docx/quy_trinh_tich_hop-noidia.pdf).

##### Kiểm tra trạng thái giao dịch:

```php
$response = $onePay->queryTransaction([
    'vpc_MerchTxnRef' => '123',
])->send();

if ($response->isSuccessful()) {
    //xử lý kết quả và hiển thị.

    var_dump($response->getData()); // toàn bộ data do OnePay gửi về.
    
} else {

    echo $response->getMessage();
}
```

Kham khảo thêm các tham trị khi tạo yêu cầu và OnePay trả về tại [đây](https://mtf.onepay.vn/developer/resource/documents/docx/quy_trinh_tich_hop-noidia.pdf).

##### Phương thức hổ trợ debug:

Một số phương thức chung hổ trợ debug khi `isSuccessful()` trả về `FALSE`:

```php
    echo $response->getCode(); // mã báo lỗi do OnePay gửi sang.
    echo $response->getMessage(); // câu thông báo lỗi do OnePay gửi sang.
```

Kham khảo bảng báo lỗi `getCode()` chi tiết tại [đây](https://mtf.onepay.vn/developer/resource/documents/docx/quy_trinh_tich_hop-noidia.pdf).

#### Quốc tế
##### Khởi tạo gateway:

```php
use NINA\NINAGateway\Facade\Gateway;

$onePay = Gateway::gateway('\NINA\NINAGateway\OnePay\InternationalGateway');
$onePay->initialize(config('gateways.gateways.OnePayInternational.options'));

```
Gateway khởi tạo ở trên dùng để tạo các yêu cầu xử lý đến OnePay hoặc dùng để nhận yêu cầu do OnePay gửi đến.

**🔴Các mục khác giống cổng nội địa**

### VTCPay
#### Khởi tạo gateway:

```php
$vtcPay = Gateway::gateway('\NINA\NINAGateway\VTCPay\Gateway');
$vtcPay->initialize(config('gateways.gateways.VTCPay.options'));
```
Gateway khởi tạo ở trên dùng để tạo các yêu cầu xử lý đến VTCPay hoặc dùng để nhận yêu cầu do VTCPay gửi đến.

### Tạo yêu cầu thanh toán:

```php
$response = $vtcPay->purchase([
    'reference_number' => time(),
    'currency' => 'VND',
    'amount' => 200000,
    'url_return' =>'https://ninaphp.info/'
])->send();

if ($response->isRedirect()) {
    $redirectUrl = $response->getRedirectUrl();
    
    // chuyển khách sang trang VTCPay để thanh toán
}
```

Kham khảo thêm các tham trị khi tạo yêu cầu và VTCPay trả về tại [đây](https://vtcpay.vn/tai-lieu-tich-hop-website).

### Kiểm tra thông tin `url_return` khi khách được VTCPay redirect về:

```php
$response = $vtcPay->completePurchase()->send();

if ($response->isSuccessful()) {
    //xử lý kết quả và hiển thị.
    echo $response->amount;
    echo $response->reference_number;
    
    var_dump($response->getData()); // toàn bộ data do VTCPay gửi sang.
    
} else {

    echo $response->getMessage();
}
```

Kham khảo thêm các tham trị khi VTCPay trả về tại [đây](https://vtcpay.vn/tai-lieu-tich-hop-website).


### Kiểm tra thông tin `IPN` do VTCPay gửi sang:

```php
$response = $vtcPay->notification()->send();

if ($response->isSuccessful()) {
    //xử lý kết quả.
    echo $response->amount;
    echo $response->reference_number;
    
    var_dump($response->getData()); // toàn bộ data do VTCPay gửi sang.
    
} else {

    echo $response->getMessage();
}
```
