---
title: "Deliverable 4 : Website"
date: "2025-06-2"
updated: "2025-06-2"
categories:
  - "sveltekit"
  - "markdown"
  - "svelte"
coverImage: "/images/linus-nylund-Q5QspluNZmM-unsplash.jpg"
coverWidth: 16
coverHeight: 9
excerpt: This post demonstrates how to include a Svelte component in a Markdown post.
---


#  Đề tài: Thiết kế và triển khai hệ thống web bán giày phân tán sử dụng ScyllaDB

## Mục đích
Ứng dụng này cần thực hiện các phương thức phân tán đã học trong lớp và áp dụng các khái niệm như fault tolerance, phân mảnh (sharding) hoặc sao chép (replication), giao tiếp phân tán,...

## 1. Fault Tolerance

### 1.1. Mục tiêu
**Fault Tolerance** (chịu lỗi) là khả năng giúp hệ thống tiếp tục hoạt động ngay cả khi một phần của hệ thống bị lỗi. Đối với hệ thống web bán hàng, điều này đặc biệt quan trọng để tránh mất dữ liệu và duy trì trải nghiệm người dùng.

### 1.2. Đề xuất và ý tưởng

#### 1.2.1. Đề xuất ý tưởng
Giả sử chúng ta có 2 server ScyllaDB:

| Tên server        | IP             | Vai trò                  |
| ----------------- | -------------- | ------------------------ |
| scylla_node_1     | 192.168.1.10   | Server chính (primary)   |
| scylla_node_2     | 192.168.1.11   | Server dự phòng (backup) |

Python app sẽ cố gắng lấy dữ liệu từ `scylla_node_1`, nếu lỗi → tự động chuyển sang `scylla_node_2`.

#### 1.2.2. Ý tưởng tổng thể
- Nhóm sẽ sử dụng ScyllaDB driver với cấu hình danh sách node.
- Nếu truy cập thất bại (server chính chết, timeout, hoặc mất kết nối), hệ thống sẽ fallback sang server dự phòng để lấy dữ liệu.
- Điều này đảm bảo ứng dụng vẫn trả về dữ liệu cho người dùng → không gián đoạn dịch vụ.

### 1.3. Chi tiết các bước thực hiện Fault Tolerance
**Bước 1:** Cấu hình Python app sử dụng ScyllaDB cluster

```python
from cassandra.cluster import Cluster

cluster = Cluster(['192.168.1.10', '192.168.1.11'])
session = cluster.connect('shoe_store')
```

**Bước 2:** Viết code xử lý fallback thủ công

```python
import logging

def get_product_from_scylla(product_id):
    try:
        product = session.execute_one("SELECT * FROM shoes WHERE id = %s", [product_id])
    except Exception as e:
        logging.warning("Lỗi truy cập ScyllaDB chính, dùng server dự phòng", exc_info=e)
        product = session.execute_one("SELECT * FROM shoes WHERE id = %s", [product_id])

    return product
```

**Tóm tắt nguyên lý hoạt động:**

| Giai đoạn      | Diễn giải                                               |
| -------------- | ------------------------------------------------------- |
| Truy cập chính  | Python app kết nối `192.168.1.10` để lấy dữ liệu sản phẩm |
| Xử lý lỗi      | Nếu gặp lỗi, sẽ ghi log và fallback sang `192.168.1.11` |
| Hoạt động tiếp | Dù server chính lỗi, hệ thống vẫn chạy mượt mà          |

### 1.4. Kiểm thử chịu lỗi
Nhóm sẽ mô phỏng đơn giản bằng cách:

- Tắt ScyllaDB chính:
```bash
systemctl stop scylla  # trên máy 192.168.1.10
```
- Gửi nhiều request đến hàm `get_product_from_scylla()`.
- Python app sẽ tự động sử dụng server backup và ghi log.

**Xem log:**
```bash
tail -f /var/log/app.log
```
Bạn sẽ thấy log cảnh báo khi gặp lỗi và fallback sang server dự phòng.

## 2. Distributed Communication

### 2.1. Mục tiêu
Các nút/ quá trình trong hệ thống phải giao tiếp với nhau qua các giao thức mạng thực tế như HTTP, gRPC, messaging, v.v. Phải hoạt động được trên nhiều máy, không chạy được trên chỉ một máy.

### 2.2. Mô hình hệ thống phân tán

| Thành phần                | Mô tả                              |
| ------------------------- | ---------------------------------- |
| Python App A (Machine 1)  | Gửi request HTTP (API) đến App B   |
| Python App B (Machine 2)  | Nhận request, xử lý và trả kết quả |
| ScyllaDB cluster          | Lưu trữ dữ liệu giày, đơn hàng     |

Hai máy độc lập, mỗi máy chạy một app Python riêng biệt, giao tiếp thông qua HTTP.

### 2.3. Ý tưởng
Python App A (giao diện người dùng hoặc API gateway) sẽ gửi request HTTP (qua requests) tới App B để yêu cầu xử lý dữ liệu hoặc lấy thông tin.

**Tình huống ví dụ:**
- App A gửi `product_id` sang App B để App B truy xuất thông tin sản phẩm từ ScyllaDB, rồi gửi kết quả lại.

### 2.4. Chi tiết thực hiện
**Bước 1:** App B viết API endpoint để nhận request

```python
from fastapi import FastAPI, HTTPException
from cassandra.cluster import Cluster

app = FastAPI()

cluster = Cluster(['192.168.1.10', '192.168.1.11'])
session = cluster.connect('shoe_store')

@app.get("/api/shoes/{id}")
def get_shoe(id: int):
    shoe = session.execute_one("SELECT * FROM shoes WHERE id = %s", [id])
    if not shoe:
        raise HTTPException(status_code=404, detail="Không tìm thấy sản phẩm")
    return {"shoe": shoe}
```

**Bước 2:** App A gửi request HTTP đến App B

Cài đặt requests:
```bash
pip install requests
```
Gửi request đến App B:
```python
import requests

def fetch_shoe_from_remote(shoe_id):
    try:
        response = requests.get(f"http://192.168.1.20/api/shoes/{shoe_id}")
        response.raise_for_status()
        return response.json()["shoe"]
    except requests.exceptions.RequestException as e:
        logging.error("Lỗi giao tiếp HTTP với App B", exc_info=e)
        return None
```

**Tóm tắt nguyên lý hoạt động:**

| Bước | Python App A (Máy 1)                | Python App B (Máy 2)                   |
| ---- | ------------------------------------ | --------------------------------------- |
| 1    | Gửi HTTP GET tới `/api/shoes/{id}`  | Nhận request qua endpoint API          |
| 2    | Dùng requests để gửi request        | Xử lý & lấy dữ liệu từ ScyllaDB        |
| 3    | Nhận JSON kết quả                    | Trả dữ liệu JSON                       |
| 4    | Hiển thị / xử lý dữ liệu             | -                                      |

### 2.5. Làm sao để đảm bảo hoạt động trên nhiều máy
Python App A và App B được deploy trên hai máy khác nhau, ví dụ:

| Python App | IP           |
| ---------- | ------------ |
| App A      | 192.168.1.19 |
| App B      | 192.168.1.20 |

- App A cần biết địa chỉ IP của App B (cấu hình trong config file).
- Hệ thống cần đảm bảo:

```bash
ping 192.168.1.20
curl http://192.168.1.20/api/shoes/1
```
Nếu lệnh này chạy được thì giao tiếp HTTP phân tán đã hoàn chỉnh.

## 3. Sharding

### 3.1. Mục tiêu
Nhóm triển khai Sharding – phân mảnh dữ liệu, tức là chia nhỏ dữ liệu ra nhiều nút ScyllaDB khác nhau, nhóm chọn Sharding vì:
- ScyllaDB hỗ trợ tính năng Sharding tích hợp sẵn.
- Sharding giúp tăng hiệu năng, giảm tải cho từng node ScyllaDB.

### 3.2. Ý tưởng tổng quát
Giả sử có 2 node ScyllaDB:

| Server | IP           |
| ------ | ------------ |
| A      | 192.168.1.10 |
| B      | 192.168.1.11 |

Chúng ta muốn lưu các dữ liệu sản phẩm, đơn hàng, người dùng... nhưng chia đều theo khóa (sharding) như sau:
- Các ID chẵn (2, 4, 6, ...) lưu ở Server A
- Các ID lẻ (1, 3, 5, ...) lưu ở Server B

Điều này giúp phân phối dữ liệu đều hơn, tránh quá tải một server.

### 3.3. Cách triển khai
Python app sử dụng driver Cassandra (tương thích với ScyllaDB) để kết nối đến cụm ScyllaDB. Để shard dữ liệu, chúng ta sẽ sử dụng logic phân chia dữ liệu dựa trên ID.

**Bước 1:** Cấu hình kết nối ScyllaDB

```python
from cassandra.cluster import Cluster

cluster = Cluster(['192.168.1.10', '192.168.1.11'])
session = cluster.connect('shoe_store')
```

**Bước 2:** Viết logic phân chia dữ liệu trong API endpoint

```python
@app.get("/api/shoes/{id}")
def get_shoe(id: int):
    table = "shoes_even" if id % 2 == 0 else "shoes_odd"
    shoe = session.execute_one(f"SELECT * FROM {table} WHERE id = %s", [id])

    if not shoe:
        raise HTTPException(status_code=404, detail="Không tìm thấy sản phẩm")

    return {"shoe": shoe}
```

**Bảng tóm tắt hoạt động:**

| Thành phần                | Vai trò chính                                      |
| ------------------------- | -------------------------------------------------- |
| `"shoes_even" if id % 2 == 0 else "shoes_odd"` | Xác định bảng dựa trên ID chẵn/lẻ |
| `session.execute_one(f"SELECT * FROM {table} ...")` | Truy vấn dữ liệu từ đúng bảng |
| `WHERE id = %s`           | Lọc theo ID sản phẩm                              |

**Bước 3:** Tạo bảng sharded trong ScyllaDB

```sql
CREATE TABLE shoes_even (
    id int,
    name text,
    brand text,
    model text,
    price int,
    description text,
    images list<text>,
    detail_url text,
    PRIMARY KEY (id)
) WITH CLUSTERING ORDER BY (id ASC);

CREATE TABLE shoes_odd (
    id int, 
    name text,
    brand text,
    model text,
    price int,
    description text,
    images list<text>,
    detail_url text,
    PRIMARY KEY (id)
) WITH CLUSTERING ORDER BY (id ASC);
```

Như vậy, dữ liệu sẽ được lưu vào các bảng `shoes_even` và `shoes_odd` tương ứng với ID chẵn hoặc lẻ.

### 3.4. Kiểm thử Sharding
Kiểm tra dữ liệu được lưu đúng bảng:

- Trên máy 192.168.1.10 (chứa chẵn):
```bash
cqlsh -e "SELECT COUNT(*) FROM shoes_even;"
```
- Trên máy 192.168.1.11 (chứa lẻ):
```bash
cqlsh -e "SELECT COUNT(*) FROM shoes_odd;"
```
Khi lưu sản phẩm có ID chẵn, nó sẽ chỉ xuất hiện ở bảng `shoes_even`, còn ID lẻ thì ở `shoes_odd`.

## 4. Memcached Sharding & Logging

### 4.1. Mục tiêu
- Lưu cache thông tin sản phẩm giày với sharding theo ID (chẵn/lẻ).
- Ghi log chi tiết thao tác cache (set/get) vào file `memcached.log`.
- Có CLI để kiểm tra trạng thái các shard Memcached.
- Giao diện web hiển thị danh sách sản phẩm, xem thông tin, cache sản phẩm và trạng thái cache.

### 4.2. Cấu trúc & Thành phần chính

| Thành phần                | Mô tả                              |
| ------------------------- | ---------------------------------- |
| MemcachedShardManager     | Quản lý việc set/get cache với phân shard theo key chẵn/lẻ, ghi log thao tác |
| Logging                   | Ghi log chi tiết thao tác set/get cache vào file riêng (memcached.log) |
| CLI command               | `python manage.py shard_status` kiểm tra trạng thái shard Memcached |
| Web Controller            | Hiển thị danh sách giày, cache sản phẩm, và trạng thái cache hiện tại |
| Template                  | Giao diện web đơn giản để tương tác với sản phẩm và cache |
| Model Product             | Mô hình dữ liệu sản phẩm giày (có thể dùng dữ liệu mẫu) |

### 4.3. Chi tiết triển khai

#### 4.3.1. MemcachedShardManager.py
```python
import logging
import pymemcache

class MemcachedShardManager:
    def __init__(self):
        # Giả sử config 2 client memcached cho chẵn/lẻ
        self.clients = {
            'even': pymemcache.Client(('192.168.1.10', 11211)),
            'odd': pymemcache.Client(('192.168.1.11', 11211))
        }

    def set(self, key, value, ttl=3600):
        client = self.get_client_by_key(key)
        success = client.set(key, value, expire=ttl)

        logging.info(f"SET [{key}] vào {self.get_shard_name(key)} - Thành công: {success}")

        return success

    def get(self, key):
        client = self.get_client_by_key(key)
        result = client.get(key)

        logging.info(f"GET [{key}] từ {self.get_shard_name(key)} - {'Không tìm thấy' if result is None else 'Đã lấy'}")

        return result

    def get_client_by_key(self, key):
        key_id = int(key.split('_')[1])
        return self.clients['even'] if key_id % 2 == 0 else self.clients['odd']

    def get_shard_name(self, key):
        key_id = int(key.split('_')[1])
        return 'Server A (chẵn)' if key_id % 2 == 0 else 'Server B (lẻ)'
```

#### 4.3.2. Logging config (settings.py)
```python
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'memcached': {
            'level': 'INFO',
            'class': 'logging.FileHandler',
            'filename': 'memcached.log',
        },
    },
    'loggers': {
        'memcached': {
            'handlers': ['memcached'],
            'level': 'INFO',
            'propagate': True,
        },
    },
}
```

#### 4.3.3. CLI command kiểm tra trạng thái shard
```python
# app/management/commands/shard_status.py
from django.core.management.base import BaseCommand
from pymemcache.client.base import Client

class Command(BaseCommand):
    help = 'Kiểm tra trạng thái cache trên các shard Memcached'

    def handle(self, *args, **options):
        clients = {
            'Server A (chẵn)': Client(('192.168.1.10', 11211)),
            'Server B (lẻ)': Client(('192.168.1.11', 11211))
        }

        self.stdout.write("Kiểm tra trạng thái cache các shard Memcached:")

        for name, client in clients.items():
            stats = client.stats()
            curr_items = int(stats.get(b'curr_items', b'0'))
            bytes_used = int(stats.get(b'bytes', b'0'))
            uptime = int(stats.get(b'uptime', b'0'))

            self.stdout.write(f" - {name}:")
            self.stdout.write(f"   + Items hiện tại: {curr_items}")
            self.stdout.write(f"   + Dung lượng (bytes): {bytes_used}")
            self.stdout.write(f"   + Thời gian chạy (giây): {uptime}")

        return 0
```

#### 4.3.4. Web Controller (ví dụ ProductController.py)
```python
from django.shortcuts import render, get_object_or_404
from .models import Product
from .services.MemcachedShardManager import MemcachedShardManager

class ProductController(object):
    def __init__(self):
        self.cache = MemcachedShardManager()

    # Danh sách giày mẫu
    products = {
        1: {'name': 'Giày Thể Thao A', 'price': 1200000},
        2: {'name': 'Giày Sneaker B', 'price': 1500000},
        3: {'name': 'Giày Cao Gót C', 'price': 1800000},
        4: {'name': 'Giày Lười D', 'price': 1100000},
    }

    def index(self, request):
        return render(request, 'products/index.html', {'products': self.products})

    def show(self, request, id):
        key = f"product_{id}"
        product = self.cache.get(key)

        if not product:
            if id not in self.products:
                return get_object_or_404(Product, id=id)

            product = self.products[id]
            self.cache.set(key, product, 3600)

        return render(request, 'products/show.html', {'product': product, 'id': id})
```

#### 4.3.5. URLs (urls.py)
```python
from django.urls import path
from .views import ProductController

urlpatterns = [
    path('products', ProductController.as_view('index'), name='products.index'),
    path('productsDưới đây là phần tiếp theo của nội dung, bao gồm phần URLs và các template cho giao diện web:
```
```python
    path('products/<int:id>', ProductController.as_view('show'), name='products.show'),
]
```

### 4.3.6. Template ví dụ

**products/index.html**
```html
<!DOCTYPE html>
<html>
<head>
    <title>Danh sách Giày</title>
</head>
<body>
    <h1>Danh sách Giày Bán</h1>
    <ul>
        {% for id, product in products.items %}
            <li>
                <a href="{% url 'products.show' id %}">{{ product.name }}</a> - {{ product.price|floatformat:0 }} VND
            </li>
        {% endfor %}
    </ul>
</body>
</html>
```

**products/show.html**
```html
<!DOCTYPE html>
<html>
<head>
    <title>Chi tiết Giày</title>
</head>
<body>
    <h1>{{ product.name }}</h1>
    <p>Giá: {{ product.price|floatformat:0 }} VND</p>
    <p><a href="{% url 'products.index' %}">Quay lại danh sách</a></p>
</body>
</html>
```

### 4.4. Tóm tắt kết quả

| Hoạt động                     | Mô tả                                                                 |
|------------------------------|----------------------------------------------------------------------|
| Truy cập `/products`         | Hiển thị danh sách sản phẩm giày.                                   |
| Truy cập `/products/{id}`    | Lấy thông tin giày từ cache, nếu chưa có thì load từ mảng mẫu, đồng thời lưu cache và ghi log. |
| File log `memcached.log`     | Ghi chi tiết set/get cache kèm shard (chẵn/lẻ).                   |
| CLI `python manage.py shard_status` | Kiểm tra số lượng key cache trên từng server shard.              |

Bạn có thể mở rộng thêm các tính năng như:
- Xóa cache sản phẩm.
- Thống kê số lượt truy cập sản phẩm.
- Giao diện quản lý log hoặc trạng thái cache chi tiết hơn.

## 5. Memcached Sharding + Stress Test

### 5.1. Mô tả chức năng

| Tính năng                    | Mô tả                                                                 |
|-----------------------------|----------------------------------------------------------------------|
| Trang danh sách giày        | Hiển thị các sản phẩm giày mẫu.                                    |
| Trang chi tiết giày         | Hiển thị chi tiết từng sản phẩm, cache thông tin sản phẩm trên shard phù hợp. |
| Endpoint stress test `/stress/{id}` | Ghi và đọc cache cho sản phẩm với ID truyền vào, dùng để test tải. |
| Logging chi tiết thao tác cache | Ghi log mỗi lần set/get cache vào file `memcached.log`.           |
| CLI `artisan shard:status`  | Kiểm tra trạng thái cache (số lượng key trên từng shard Memcached). |

### 5.2. Mã nguồn đầy đủ

#### 5.2.1. routes/web.php
```php
use App\Http\Controllers\ProductController;

Route::get('/products', [ProductController::class, 'index'])->name('products.index');
Route::get('/products/{id}', [ProductController::class, 'show'])->name('products.show');

// Endpoint stress test
Route::get('/stress/{id}', function ($id) {
    $cache = new \App\Services\MemcachedShardManager();

    $key = "product_$id";
    $value = ['name' => 'Giày Test Stress', 'price' => rand(500000, 2000000)];

    $cache->set($key, $value);
    $result = $cache->get($key);

    return response()->json(['cached' => $result]);
});
```

#### 5.2.2. app/Services/MemcachedShardManager.php
```php
<?php

namespace App\Services;

use Illuminate\Support\Facades\Log;

class MemcachedShardManager
{
    protected $clients;

    public function __construct()
    {
        $this->clients = [
            'even' => new \Memcached(),
            'odd'  => new \Memcached(),
        ];
        $this->clients['even']->addServer('192.168.1.10', 11211);
        $this->clients['odd']->addServer('192.168.1.11', 11211);
    }

    public function set($key, $value, $ttl = 3600)
    {
        $client = $this->getClientByKey($key);
        $success = $client->set($key, $value, $ttl);

        Log::channel('memcached')->info("SET [$key] vào " . $this->getShardName($key) . " - " . ($success ? 'THÀNH CÔNG' : 'THẤT BẠI'));

        return $success;
    }

    public function get($key)
    {
        $client = $this->getClientByKey($key);
        $result = $client->get($key);

        Log::channel('memcached')->info("GET [$key] từ " . $this->getShardName($key) . " - " . (is_null($result) ? "KHÔNG TÌM THẤY" : "ĐÃ LẤY"));

        return $result;
    }

    protected function getClientByKey($key)
    {
        $id = (int) filter_var($key, FILTER_SANITIZE_NUMBER_INT);
        return ($id % 2 === 0) ? $this->clients['even'] : $this->clients['odd'];
    }

    protected function getShardName($key)
    {
        $id = (int) filter_var($key, FILTER_SANITIZE_NUMBER_INT);
        return ($id % 2 === 0) ? 'Server A (chẵn)' : 'Server B (lẻ)';
    }
}
```

#### 5.2.3. config/logging.php (thêm channel mới)
```php
'memcached' => [
    'driver' => 'single',
    'path' => storage_path('logs/memcached.log'),
    'level' => 'info',
],
```

#### 5.2.4. CLI kiểm tra trạng thái shard app/Console/Commands/ShardStatusCommand.php
```php
<?php

namespace App\Console\Commands;

use Illuminate\Console\Command;

class ShardStatusCommand extends Command
{
    protected $signature = 'shard:status';
    protected $description = 'Kiểm tra trạng thái cache trên các shard Memcached';

    protected $clients;

    public function __construct()
    {
        parent::__construct();

        $this->clients = [
            'Server A (chẵn)' => new \Memcached(),
            'Server B (lẻ)'   => new \Memcached(),
        ];
        $this->clients['Server A (chẵn)']->addServer('192.168.1.10', 11211);
        $this->clients['Server B (lẻ)']->addServer('192.168.1.11', 11211);
    }

    public function handle()
    {
        $this->info("Kiểm tra trạng thái cache các shard Memcached:");

        foreach ($this->clients as $name => $client) {
            $stats = $client->getStats();
            $info = reset($stats);

            $curr_items = $info['curr_items'] ?? 0;
            $bytes_used = $info['bytes'] ?? 0;
            $uptime = $info['uptime'] ?? 0;

            $this->line(" - $name:");
            $this->line("   + Items hiện tại: $curr_items");
            $this->line("   + Dung lượng (bytes): $bytes_used");
            $this->line("   + Thời gian chạy (giây): $uptime");
        }

        return 0;
    }
}
```

#### 5.2.5. app/Http/Controllers/ProductController.php
```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Services\MemcachedShardManager;

class ProductController extends Controller
{
    protected $cache;
    protected $products;

    public function __construct()
    {
        $this->cache = new MemcachedShardManager();

        $this->products = [
            1 => ['name' => 'Giày Thể Thao A', 'price' => 1200000],
            2 => ['name' => 'Giày Sneaker B', 'price' => 1500000],
            3 => ['name' => 'Giày Cao Gót C', 'price' => 1800000],
            4 => ['name' => 'Giày Lười D', 'price' => 1100000],
        ];
    }

    public function index()
    {
        return view('products.index', ['products' => $this->products]);
    }

    public function show($id)
    {
        $key = "product_$id";
        $product = $this->cache->get($key);

        if (!$product) {
            if (!isset($this->products[$id])) {
                abort(404, 'Sản phẩm không tồn tại');
            }

            $product = $this->products[$id];
            $this->cache->set($key, $product, 3600);
        }

        return view('products.show', ['product' => $product, 'id' => $id]);
    }
}
```

#### 5.2.6. Views
**resources/views/products/index.blade.php**
```html
<!DOCTYPE html>
<html>
<head>
    <title>Danh sách Giày</title>
</head>
<body>
    <h1>Danh sách Giày Bán</h1>
    <ul>
        @foreach ($products as $id => $product)
            <li>
                <a href="{{ route('products.show', $id) }}">{{ $product['name'] }}</a> - {{ number_format($product['price']) }} VND
            </li>
        @endforeach
    </ul>
</body>
</html>
```

**resources/views/products/show.blade.php**
```html
<!DOCTYPE html>
<html>
<head>
    <title>Chi tiết Giày</title>
</head>
<body>
    <h1>{{ $product['name'] }}</h1>
    <p>Giá: {{ number_format($product['price']) }} VND</p>
    <p><a href="{{ route('products.index') }}">Quay lại danh sách</a></p>
</body>
</html>
```

### 5.3. Stress test & Kiểm tra log
**Stress test (giả lập):**
```bash
ab -n 500 -c 50 http://localhost:5000/stress/2
```

**Theo dõi log cache:**
```bash
tail -f logs/memcached.log
```

### 5.4. Kết luận
**Ứng dụng demo:**
- Vận hành phân mảnh Memcached theo key chẵn/lẻ.
- Ghi log chi tiết thao tác cache.
- Cung cấp endpoint stress test đơn giản.
- CLI giám sát trạng thái cache từng shard.

**Đáp ứng mục tiêu test tải, giám sát và phát hiện lỗi dễ dàng.**

Bạn chỉ cần chuẩn bị 2 server Memcached tại IP `192.168.1.10` & `192.168.1.11` hoặc thay bằng IP thật phù hợp nhé.

## 6. System Recovery (Rejoin after Failure)
### Lý do chọn:
Trong hệ thống phân tán với ScyllaDB, các nút có thể gặp lỗi hoặc khởi động lại bất cứ lúc nào. Khả năng tự động tham gia lại (rejoin) giúp hệ thống duy trì tính sẵn sàng cao, tránh downtime và đảm bảo dữ liệu đồng bộ sau khi nút bị lỗi.

### Áp dụng trong hệ thống:
Khi một nút bị lỗi hoặc restart, nó sẽ tự động đồng bộ dữ liệu với cluster hiện tại và tham gia lại quá trình phục vụ yêu cầu.

### Cơ chế:
Gossip protocol của ScyllaDB hỗ trợ cập nhật trạng thái các nút để tái thiết lập cluster.

### Lợi ích:
Tăng tính ổn định và độ bền hệ thống, giảm thiểu gián đoạn dịch vụ.

## 7. Load Balancing
### Lý do chọn:
Web bán hàng cần xử lý nhiều yêu cầu đồng thời từ khách hàng. Phân phối tải đều giữa các nút backend và nút database giúp tránh tình trạng quá tải nút đơn lẻ, cải thiện hiệu năng tổng thể và giảm độ trễ.

### Áp dụng trong hệ thống:
Sử dụng Load Balancer (ví dụ Nginx hoặc HAProxy) phân phối yêu cầu HTTP giữa các instance backend Node.js.

### Lợi ích:
Tăng khả năng mở rộng, tối ưu tài nguyên phần cứng, nâng cao trải nghiệm người dùng.

## 8. Demo hệ thống

### Video demo:
<video width="640" controls>
  <source src="/video/Demo%202025.06.02%20-%2002.16.02.01.mp4" type="video/mp4">
  Trình duyệt không hỗ trợ video.
</video>

## 9. Code Snippets tiêu biểu

### Tìm kiếm sản phẩm:
```html
<script>
  const search = () => {
    const searchbox = document.getElementById("search-input").value.toUpperCase();
    const product = document.querySelectorAll(".hang11");
    const pname = document.getElementsByTagName("h3");
    for (var i = 0; i < pname.length; i++) {
      let match = product[i].getElementsByTagName('h3')[0];
      if (match) {
        let textvalue = match.textContent || match.innerHTML;
        product[i].style.display = (textvalue.toUpperCase().indexOf(searchbox) > -1) ? "" : "none";
      }
    }
  }
</script>
```

### FastAPI kết nối ScyllaDB:
```python
@app.get("/shoes")
async def get_shoes():
    rows = session.execute("SELECT * FROM shoes LIMIT 100")
    return JSONResponse(content=[{
        "id": str(row.id),
        "name": row.name,
        "brand": row.brand,
        "model": row.model,
        "price": row.price,
        "description": row.description,
        "images": row.images,
        "detail_url": row.detail_url
    } for row in rows])
```

## 10. Kế hoạch tiếp theo
- Tích hợp Load Balancer (dự kiến: HAProxy hoặc thuật toán tùy chỉnh).
- Hoàn thiện Fault Tolerance + thử nghiệm các lỗi thực tế.
- Triển khai Leader Election và cơ chế System Recovery.
- Thực hiện Stress Test với nhiều yêu cầu đồng thời.
- Viết báo cáo PDF + Video demo.
- Đảm bảo phân công rõ ràng, commit đầy đủ trên GitHub.
