# Hướng Dẫn Sử Dụng Hadoop Cluster

[![Hadoop](https://img.shields.io/badge/Hadoop-3.4-orange)](https://hub.docker.com/r/apache/hadoop)
[![Spark](https://img.shields.io/badge/Spark-4.1.1-red)](https://hub.docker.com/r/apache/spark)
[![Docker](https://img.shields.io/badge/Docker-Compose-blue)](https://docs.docker.com/compose/)
[![Status](https://img.shields.io/badge/Status-Production%20Ready-green)]()

Hệ thống xử lý dữ liệu lớn với Hadoop và Spark, sẵn sàng sử dụng với Docker. Hướng dẫn này dành cho người dùng cuối.

---

## 📖 Giới Thiệu

Đây là hệ thống xử lý dữ liệu lớn (Big Data) cho phép bạn:
- ✅ Lưu trữ file dữ liệu lớn.
- ✅ Xử lý và phân tích dữ liệu bằng Python.
- ✅ Mô phỏng chạy các tác vụ phân tán trên nhiều máy.
- ✅ Truy cập dữ liệu qua giao diện web.

**Không cần cài đặt Hadoop hay Spark trực tiếp - tất cả chạy trong Docker!**

---

## 🚀 Bắt Đầu Sử Dụng

### Bước 1: Chuẩn Bị

Đảm bảo bạn đã cài đặt:
- ✅ Docker Desktop (phải đang chạy)
- ✅ Ít nhất 4GB RAM trống
- ✅ Khoảng 5GB dung lượng ổ cứng

**Khuyến nghị:** Tạo thư mục `data/` trong project để dễ upload file:
```bash
mkdir data
```

### Bước 2: Khởi Động Hệ Thống

Mở Terminal/Command Prompt tại thư mục dự án và chạy:

```bash
# Khởi động hệ thống (mất khoảng 60 giây)
docker compose up -d
```

**Đợi 60 giây** để hệ thống khởi động hoàn toàn, sau đó **tùy chọn** khởi tạo:

```bash
# Vào container hadoop-client
docker compose exec -it hadoop-client bash

# (Tùy chọn) Tạo cấu trúc thư mục đề xuất:
hdfs dfs -mkdir -p /data/raw /data/processed /data/backup

# Kiểm tra:
hdfs dfs -ls /
```

**💡 Lưu ý:** Trong thực tế, HDFS tự động tạo thư mục khi bạn upload file. Bước này chỉ để có cấu trúc rõ ràng cho việc học tập.

**📁 Thư mục script:** `/spark-apps/` (tương ứng với `./spark-apps/` trên máy tính)

### Bước 3: Kiểm Tra Hệ Thống

Mở trình duyệt và truy cập:
- **Quản lý file:** http://localhost:9870
- **Quản lý tác vụ:** http://localhost:8088

Nếu thấy giao diện web là hệ thống đã hoạt động! 🎉

---

## 💡 Cách Sử Dụng Lệnh

**Tất cả lệnh dưới đây cần chạy bên trong terminal của container. Cách làm:**

```bash
# 1. Vào container hadoop-client (để quản lý HDFS, file operations)
docker compose exec -it hadoop-client bash

# 2. Hoặc vào container spark-master (để chạy Python/PySpark)
docker compose exec -it spark-master bash

# 3. Chạy các lệnh trong terminal của container

# 4. Thoát container khi xong
exit
```

**Ghi chú:** Các phần dưới đây sẽ chỉ rõ nên dùng container nào.

### 🔍 Cách Nhận Biết Đang Ở Đâu

**Bên trong container (sau khi chạy `docker compose exec -it ... bash`):**
```
root@hadoop-client:/# 
root@spark-master:/#
```
👉 Prompt có dạng `root@[tên-container]:/#` - bạn có thể chạy lệnh HDFS, Python

**Bên ngoài (terminal của máy tính):**
```
PS C:\Users\YourName\project>   # Windows
$ ~/project                      # Mac/Linux
```
---

## 💼 Các Tác Vụ Thường Dùng

### 📤 Upload File Lên Hệ Thống

**Container:** `hadoop-client`

**Tình huống:** Bạn có file `data.csv` trên máy tính và muốn upload lên hệ thống.

#### 🎯 Dùng Thư Mục Chia Sẻ `./data/`

Hệ thống đã tự động chia sẻ thư mục `data/` giữa máy tính và container.

```bash
# Bước 1: Copy file vào thư mục data/ trong project
# (bạn có thể kéo thả file vào thư mục này)

# Bước 2: Kiểm tra file đã có
ls -lh /data-local/

# Bước 3: Upload file lên HDFS
hdfs dfs -put /data-local/data.csv /data/raw/

# Bước 4: Kiểm tra file đã lên HDFS
hdfs dfs -ls /data/raw/
```

**Ưu điểm:**
- ✅ Không cần lệnh `docker cp`
- ✅ File trong thư mục `data/` tự động xuất hiện trong container
- ✅ Dễ quản lý nhiều file cùng lúc
- ✅ Có thể kéo thả file bằng chuột trên Windows

**Lưu ý:** Nếu thư mục `data/` chưa tồn tại, tạo bằng lệnh `mkdir data`

---

### 📥 Download File Từ Hệ Thống

**Container:** `hadoop-client`

**Tình huống:** Bạn muốn tải file kết quả về máy tính.

#### 🎯 Dùng Thư Mục Chia Sẻ `./data/` (Khuyến Nghị)

```bash
# Bước 1: Download file từ HDFS về thư mục chia sẻ
hdfs dfs -get /data/processed/result.csv /data-local/

# Bước 2: Kiểm tra file đã download
ls -lh /data-local/result.csv

# Bước 3: Thoát container
exit

# File đã tự động có trong thư mục ./data/ trên máy tính của bạn!
```

**Ưu điểm:**
- ✅ File tự động xuất hiện trong thư mục `data/` trên máy
- ✅ Không cần lệnh `docker cp`
- ✅ Download nhiều file cùng lúc: `hdfs dfs -get /data/processed/* /data-local/`

---

### 📂 Xem Danh Sách File

**Container:** `hadoop-client`

```bash
# Xem tất cả file trong hệ thống
hdfs dfs -ls /

# Xem file trong thư mục cụ thể
hdfs dfs -ls /data/raw/

# Xem chi tiết dung lượng
hdfs dfs -du -h /data/
```

### 📄 Đọc Nội Dung File

**Container:** `hadoop-client`

```bash
# Đọc toàn bộ file
hdfs dfs -cat /data/raw/users.csv

# Đọc 10 dòng đầu
hdfs dfs -cat /data/raw/users.csv | head -n 10

# Đọc 10 dòng cuối
hdfs dfs -cat /data/raw/users.csv | tail -n 10
```

### 🗑️ Xóa File

**Container:** `hadoop-client`

```bash
# Xóa một file
hdfs dfs -rm /data/raw/old-file.csv

# Xóa thư mục và tất cả file bên trong
hdfs dfs -rm -r /data/old-folder/
```

### 📁 Tạo Thư Mục

**Container:** `hadoop-client`

```bash
# Tạo thư mục mới
hdfs dfs -mkdir -p /data/my-project/input
```

---

## 🐍 Xử Lý Dữ Liệu Với Python (PySpark)

### Bước 1: Tạo File Python

Tạo file `my_analysis.py` trong thư mục `spark-apps/` trên máy tính của bạn (file sẽ tự động có trong container vì đã mount volume):

```python
from pyspark.sql import SparkSession

# Khởi tạo Spark
spark = SparkSession.builder \
    .appName("My Data Analysis") \
    .master("local") \
    .getOrCreate()

# Đọc file CSV từ HDFS
df = spark.read.csv(
    "hdfs://namenode:8020/data/raw/users.csv",
    header=True,
    inferSchema=True
)

# Xem dữ liệu
print("=== Dữ liệu đầu vào ===")
df.show(10)

# Thực hiện phân tích
print("=== Thống kê ===")
df.describe().show()

# Lưu kết quả
df.write.mode("overwrite").csv(
    "hdfs://namenode:8020/data/processed/result",
    header=True
)

print("Hoàn thành!")
spark.stop()
```

### Bước 2: Chạy Script

**Container:** `spark-master`

```bash
# Chạy script
python3 /spark-apps/my_analysis.py
```

### Bước 3: Xem Kết Quả

**Container:** `hadoop-client`

```bash
hdfs dfs -ls /data/processed/result/
hdfs dfs -cat /data/processed/result/*.csv | head -n 20
```

---

## 🎯 Các Ví Dụ Thực Tế

### Ví Dụ 1: Đếm Số Dòng Trong File

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("Count Rows").master("local").getOrCreate()
df = spark.read.csv("hdfs://namenode:8020/data/raw/users.csv", header=True)

total_rows = df.count()
print(f"Tổng số dòng: {total_rows}")

spark.stop()
```

### Ví Dụ 2: Lọc Dữ Liệu

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("Filter Data").master("local").getOrCreate()
df = spark.read.csv("hdfs://namenode:8020/data/raw/users.csv", header=True)

# Lọc user có age > 25
filtered_df = df.filter(df.age > 25)
filtered_df.show()

# Lưu kết quả
filtered_df.write.mode("overwrite").csv(
    "hdfs://namenode:8020/data/processed/filtered_users",
    header=True
)

spark.stop()
```

### Ví Dụ 3: Gộp Nhiều File

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("Merge Files").master("local").getOrCreate()

# Đọc tất cả file CSV trong thư mục
df = spark.read.csv("hdfs://namenode:8020/data/raw/*.csv", header=True)

print(f"Tổng số dòng sau khi gộp: {df.count()}")

# Lưu thành 1 file duy nhất
df.coalesce(1).write.mode("overwrite").csv(
    "hdfs://namenode:8020/data/processed/merged",
    header=True
)

spark.stop()
```

---

## 🔧 Quản Lý Hệ Thống

### Tắt Hệ Thống (Giữ Dữ Liệu)

```bash
# Tạm dừng - dữ liệu vẫn còn
docker compose stop
```

### Khởi Động Lại

```bash
# Chạy lại hệ thống - dữ liệu vẫn nguyên
docker compose start
```

### Khởi Động Lại Hoàn Toàn

```bash
# Khởi động lại tất cả
docker compose restart
```

### Xóa Và Làm Mới

```bash
# Xóa containers nhưng giữ dữ liệu
docker compose down

# Xóa hoàn toàn (cả dữ liệu) - THẬN TRỌNG!
docker compose down -v
```

⚠️ **Lưu ý:** Nếu chạy `docker compose down -v`, bạn sẽ mất tất cả dữ liệu và phải chạy lại script khởi tạo.

---

## 📊 Xem Trạng Thái Hệ Thống

### Xem Dung Lượng

**Container:** `hadoop-client`

```bash
# Xem dung lượng đã dùng
hdfs dfs -df -h /

# Xem dung lượng từng thư mục
hdfs dfs -du -h /data/
```

### Kiểm Tra Các Node

**Container:** `hadoop-client`

```bash
# Xem các node đang chạy
yarn node -list

# Xem trạng thái HDFS
hdfs dfsadmin -report
```

### Xem Các Tác Vụ Đang Chạy

**Container:** `hadoop-client`

```bash
# Liệt kê tác vụ đang chạy
yarn application -list

# Hoặc truy cập: http://localhost:8088
```

---

## ❓ Xử Lý Sự Cố

### Hệ Thống Không Khởi Động

```bash
# Xem log để biết lỗi
docker compose logs namenode
docker compose logs datanode1
```

### File Upload Bị Lỗi

**Container:** `hadoop-client`

```bash
# Kiểm tra HDFS đang chạy
hdfs dfs -ls /

# Nếu báo "safe mode", chạy:
hdfs dfsadmin -safemode leave
```

### Python Script Báo Lỗi

**Container:** `spark-master`

```bash
# Kiểm tra PySpark
python3 -c "from pyspark.sql import SparkSession; print('OK')"

# Xem log chi tiết khi chạy script
python3 /spark-apps/your-script.py 2>&1 | more
```

### Giao Diện Web Không Mở

```bash
# Kiểm tra containers đang chạy
docker compose ps

# Khởi động lại nếu cần
docker compose restart namenode resourcemanager
```

---

## 🎓 Mẹo Sử Dụng

### 1. Làm Việc Với File Lớn

**Container:** `hadoop-client`

Khi upload file lớn (>1GB):
```bash
hdfs dfs -put -f /path/to/large-file.csv /data/raw/
```

### 2. Kiểm Tra Nhanh Python Script

**Container:** `spark-master`

Trước khi chạy script phức tạp, test nhanh:
```bash
python3 -c "
from pyspark.sql import SparkSession
spark = SparkSession.builder.master('local').getOrCreate()
print('Spark version:', spark.version)
spark.stop()
"
```

### 3. Backup Dữ Liệu

**Container:** `hadoop-client`

```bash
# Download toàn bộ thư mục về container
hdfs dfs -get /data/processed /tmp/backup/

# Thoát và copy về máy
exit
docker cp hadoop-client:/tmp/backup ./backup/
```

### 4. Xem Log Real-time

```bash
# Theo dõi log trực tiếp
docker compose logs -f namenode
```

---

## 📞 Trợ Giúp Thêm

### Giao Diện Web

| Trang | Địa Chỉ | Mục Đích |
|-------|---------|----------|
| Quản lý file | http://localhost:9870 | Xem file, dung lượng, trạng thái |
| Quản lý tác vụ | http://localhost:8088 | Xem job đang chạy, lịch sử |

### Các Lệnh Hữu Ích

```bash
# Copy file giữa containers
docker cp local-file.txt hadoop-client:/tmp/

# Chạy lệnh shell trong container
docker compose exec -it hadoop-client bash

# Xem IP của containers (không cần -it cho lệnh ngắn)
docker compose exec hadoop-client hostname -i
```

---

## ✅ Checklist Sử Dụng Hàng Ngày

- [ ] Kiểm tra Docker Desktop đang chạy
- [ ] Chạy `docker compose ps` để xem services đang up
- [ ] Truy cập http://localhost:9870 để xác nhận HDFS hoạt động
- [ ] Upload file dữ liệu cần xử lý
- [ ] Chạy Python script phân tích
- [ ] Xem kết quả trên HDFS hoặc download về máy
- [ ] Tắt hệ thống với `docker compose stop` khi không dùng

---

## 🎯 Workflow Tiêu Biểu

```
1. Chuẩn bị dữ liệu → data.csv
2. Upload → hdfs dfs -put data.csv /data/raw/
3. Viết script Python → my_analysis.py  
4. Chạy script → python3 my_analysis.py
5. Xem kết quả → hdfs dfs -cat /data/processed/result/*.csv
6. Download về máy → hdfs dfs -get /data/processed/result ./
```

---

**Chúc bạn sử dụng hiệu quả! 🚀**

Nếu gặp vấn đề không giải quyết được, hãy kiểm tra log với `docker compose logs [tên-service]`
