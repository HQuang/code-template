# code-teamplate

Template cơ bản cho laravel
Bắt đầu:

===== KHỞI TẠO FILE CẤU HÌNH CHUNG ======
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
Khởi tạo tên controller theo tên \$ControllerName đã cấu hình trong route
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
        return view($this->pathViewController . 'index');
    }
}
```

===== KHỞI TẠO VIEW =====

Trong folder resources/views/ khởi tạo folder con và file theo khai báo ở \$this->pathViewController . 'index'
resources/views/admin/dashboard/index.blade.php
