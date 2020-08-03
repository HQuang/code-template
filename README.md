# code-teamplate

Cài đặt composer create-project --prefer-dist laravel/laravel test_laravel
Cài đặt Laravel debugbar: https://github.com/barryvdh/laravel-debugbar

Template cơ bản cho laravel
Bắt đầu:

===== KHỞI TẠO FILE CẤU HÌNH CHUNG ======

-   Tại folder config tạo file configOptimal.php với nội dung bên dưới.
    config/configOptimal.php

```
return [
    // Cấu hình url trong site và ngoài site
    'url' => [
        'prefix_admin' => 'cpanel',
        'prefix_news' => 'news',
    ]
];
```

===== KHỞI TẠO ROUTE =====

-   Tại file web.php trong folder routes sử dụng để khai báo và sử dụng các route, khai báo với nội dung code như bên dưới.
    routes/web.php

```
use Illuminate\Support\Facades\Route;
// Lấy url cấu hình từ file config/configOptimal.php hoặc nếu null thì lấy mặc định là admin
$prefixAdmin = Config::get('configOptimal.url.prefix_admin', 'admin');

Route::get('/', function () {
    return view('welcome');
});

// Tạo nhóm url trong admin/
Route::group(['prefix' => $prefixAdmin], function () {
    Route::get('/', function () {
        return redirect()->route('dashboard');
    });

    // ================ Dashboard
    // Name module
    $prefix_module = 'dashboard';
    // Name controller
    $ModuleName = 'dashboard';
    Route::group(['prefix' => $prefix_module], function () use ($ModuleName) {
        $ControllerName = ucfirst($ModuleName) . 'Controller@';

        Route::get('/', [
            'as' => $ModuleName,
            'uses' => $ControllerName . 'index',
        ]);
    });
});
```

===== KHỞI TẠO CONTROLLER =====

-   Khởi tạo tên controller theo tên \$ControllerName đã cấu hình trong route.
    app/Http/Controllers/DashboardController.php

```
namespace App\Http\Controllers;
use App\Http\Controllers\Controller;

class DashboardController extends Controller{
    private $pathViewController = 'admin.dashboard.';
    private $controllerName = 'dashboard';

    public function __construct(){
        view()->share('controllerName', $this->controllerName);
    }

    public function index(){
        return view($this->pathViewController . 'index', ['name' => 'lê hồng quang']); // Đưa dữ liệu ra view
    }
}
```

===== KHỞI TẠO VIEW =====

-   Trong folder resources/views/ khởi tạo folder con và file theo khai báo ở \$this->pathViewController . 'index'
    resources/views/admin/dashboard/index.blade.php

===== GHÉP GIAO DIỆN BLADE =====

-   Giao diện HTML, css, js ... sẽ nằm trong folder public

-   Theo ví dụ này tạo folder admin để xây dựng cấu trúc và chứa các thành phần giao diện trang admin, copy giao diện vào folder admin này.

-   Tại folder resources/views/admin tạo file main.blade.php để chứa bố cục giao diện sử dụng chung, copy code giao diện vào file này.

-   Thêm link css, js: Trong blade template có phương asset() : dùng để liên kết đến file css, js, images.
    Ví dụ <link href="{{ asset('admin/asset/bootstrap/dist/css/bootstrap.min.css') }}" rel="stylesheet">

*   Tại resources/views/admin/dashboard/index.blade.php sử dụng @extends('admin.main') để kế thừa giao diện từ file resources/views/admin/main.blade.php.

*   Tạo các layout bố cục cho giao diện:
    Tạo folder layout trong resources/views/admin/
    Tạo các file layout như header.blade.php, footer.blade.php ... tùy vào yêu cầu bố cục của giao diện.

-   Tại resources/views/admin/main.blade.php sau khi đã xác định vị trí nội dung thì đặt thẻ @yield('content') để kế thừa nội dung của các module.

-   Ở file resources/views/admin/dashboard/index.blade.php sử dụng cặp thẻ @section và @endsection để mở và khởi tạo nội dung cần hiển thị cho module
    @section('content')
    `content`
    @endsection

-   Đối với menu: Để gắn link cho menu ta sử dụng {{ route('dashboard') }}:
    dashboard là route trong web.php

===== KHỞI TẠO MODEL =====

-   Kết nối database, mở file .env và khai báo thông tin kết nối.
-   Trong folder app/ tạo folder Models.
-   Tạo file model trong app/Models với cấu trúc tên class phải cùng với tên file. Ví dụ: file UsersModel.php thì class là UsersModel, class vừa tạo phải kế thừa Model gốc của laravel
-   Tạo model bằng artisan `php artisan make:model UsersModel`

-   Model cần có các thông tin và cấu hình như sau:

```
namespace App\Models;
use Illuminate\Database\Eloquent\Model;

class UsersModel extends Model{
    // Mặc định tên bảng sẽ là UsersModels, ở đây định nghĩa lại tên bảng để sử dụng bảng khác mặc định
    protected $table = 'users';

    // Cấu hình lại tên column thời gian lưu và thời gian cập nhật
    // tên mặc định inserted_at.
    const CREATED_AT = 'created';
    // Tên mặc định updated_at.
    const UPDATED_AT = 'modified';

    // $timestamps mặc định sẽ là true và sẽ tự động cập nhật thời gian nếu trong bảng có 2 trường thời gian tạo và thời gian cập nhật, nếu false thì không.
    public $timestamps = true;

    // Ví dụ tạo phương thức lấy danh sách users
    public function listItems()
    {
        $result = null;
        $result = UsersModel::all();

        return $result;
    }
}
```

-   Ở controller UsersController.php để sử dụng model vừa tạo cần khai báo use và gán thành tên MailModel `use App\Models\UsersModel as MainModel;`
-   Ở ví dụ này lấy danh sách users thì sử dụng phương thức all() như sau:

```
// Khởi tạo giá trị tên model đã khai báo ở trên.
$MainModel = new MainModel();
$items = $MainModel->listItems();

foreach ($items as $item) {
    echo $item->name;
}
```
