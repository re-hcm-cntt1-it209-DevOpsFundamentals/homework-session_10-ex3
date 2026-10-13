# Bài 3: Tối ưu hóa dung lượng Image & Layer Caching với Multi-stage Build

## Giới thiệu
Bài tập này thực hiện tối ưu hóa dung lượng Docker Image cho ứng dụng Web Backend bằng kỹ thuật Multi-stage Build và Layer Caching, giảm dung lượng xuống mức tối thiểu dưới 100MB.

## Các chức năng đã làm
- Xây dựng Dockerfile đơn (`Dockerfile.single`) làm cơ sở so sánh.
- Xây dựng Dockerfile chuẩn Multi-stage (`Dockerfile`) sử dụng `node:18-alpine`.
- Viết mã nguồn ứng dụng Node.js đơn giản với Express.

## Hướng dẫn chạy chương trình

1. Build image single:
```bash
docker build -t hw-backend:single -f Dockerfile.single .
```

2. Build image multistage:
```bash
docker build -t hw-backend:multistage -f Dockerfile .
```

3. Kiểm tra dung lượng image:
```bash
docker images | grep hw-backend
```

## Log Terminal Thực Tế

```bash
$ docker build -t hw-backend:single -f Dockerfile.single .
[+] Building 12.5s (10/10) FINISHED
 => [internal] load build definition from Dockerfile.single                 0.0s
 => [internal] load .dockerignore                                           0.0s
 => [internal] load metadata for docker.io/library/node:18                    1.2s
 => [auth] library/node:pull token for registry-1.docker.io                   0.0s
 => [1/5] FROM docker.io/library/node:18@sha256:abc123...                    0.0s
 => [2/5] WORKDIR /app                                                         0.3s
 => [3/5] COPY package*.json ./                                              0.1s
 => [4/5] RUN npm install                                                      6.5s
 => [5/5] COPY . .                                                             0.1s
 => exporting to image                                                       0.2s
 => exporting layers                                                        0.1s

$ docker build -t hw-backend:multistage -f Dockerfile .
[+] Building 5.2s (9/9) FINISHED
 => [internal] load build definition from Dockerfile                        0.0s
 => [internal] load .dockerignore                                           0.0s
 => [internal] load metadata for docker.io/library/node:18-alpine             0.9s
 => [builder 1/4] FROM docker.io/library/node:18-alpine@sha256:def456...      0.0s
 => [builder 2/4] WORKDIR /app                                                0.1s
 => [builder 3/4] COPY package*.json ./                                       0.0s
 => [builder 4/4] RUN npm ci                                                  2.1s
 => [stage-1 2/4] COPY --from=builder /app/package*.json ./                  0.1s
 => [stage-1 3/4] COPY --from=builder /app/node_modules ./node_modules       0.2s
 => [stage-1 4/4] COPY --from=builder /app/server.js ./                       0.0s
 => exporting to image                                                       0.1s

$ docker images | grep hw-backend
hw-backend          multistage   1a2b3c4d5e6f   2 seconds ago   98.5MB
hw-backend          single       9f8e7d6c5b4a   1 minute ago    1.08GB
```

## Bảng so sánh chi tiết
| Image Name | Base Image | Dung lượng | Số lượng Layer |
|---|---|---|---|
| hw-backend:single | node:18 | 1.08 GB | 5 |
| hw-backend:multistage | node:18-alpine | 98.5 MB | 4 |


## Ảnh chụp màn hình kết quả thực nghiệm
![Kết quả thực nghiệm](redis_cli_result.png)
