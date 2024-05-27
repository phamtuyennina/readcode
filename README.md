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
