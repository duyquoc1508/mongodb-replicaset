# Setup mongodb replica with docker

* Note: 
> Do triển khai thực tế các container chạy instance `mongo` sẽ chạy trên các host khác nhau, khác Data center thậm chí khác cả khu vực địa lý. -> File docker-compose như dưới sẽ sai so với kiến trúc triển khai trên trang chủ (chạy các node trên cùng 1 host ( mất hết ý nghĩa dự phòng, sẵn sàng cao của replSet)).

## Cách 1: Setup manually

1. Create docker network
```
docker network create my-mongo-cluster
```
2. Chạy các containers mongo
Vì ở đây chỉ cần replica demo nên chỉ cần chạy 1 container là được (for portable)
```
docker-compose up -d
```
or
```
docker-compose -f docker-compose.yml up -d
```
Kiểm tra ip các containers đang chạy trong network
```
docker network inspect my-mongo-cluster
```
2. Connect vào rồi setup replSet

- Execute vào container
```
docker exec -it mongo1 bash
```
- Config mongo
```
mongo
```
- Thêm cấu hình cho replica set
Tuỳ vào muốn setup replica bao nhiêu nodes mà khai báo bấy nhiêu member
```
> config = { "_id": "my-mongo-set", "members": [ { "_id": 0, "host": "mongo1:27017" }, { "_id": 1, "host": "mongo2:27017" }, { "_id":2, "host": "mongo3:27017" } ] }
```
- start replSet. Vẫn đang trong container mongo1. Sau khi start replSet nó sẽ chuyển trạng thái Secondary và chờ 1 lát nó sẽ trở thành Primary.
```
> rs.initiate(config)
```

## Cách 2: Setup automatically
Các setup replica được định sẵn trong docker compose
```
docker-compose -f docker-compose-auto.yml up -d
```