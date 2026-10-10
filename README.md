# Домашнее задание Сартисона Евгения N14 #

Описание/Пошаговая инструкция выполнения домашнего задания:
(1) Запустить сервис S3 в облаке или локально (Docker + minio)

(2) Создать бакет используя API

(3) Сохранить файл в бакет используя API

(4) Сохраните файл программно - выберите знакомый язык программирования (C#, Java, Go, Python или любой другой, для которого есть библиотека для работы с S3), отправьте файл в бакет и потом получите массив файлов из бакет и отобазите в бараузере или консоли.

Для пунктов 2 и 3 сделайте скриншоты результата выполнения.

Для пункта 4 приложите ссылку на репозитарий на гитхабе с исходным кодом.


## (1) Запустить сервис S3 в облаке или локально (Docker + minio) ## 

Использую пример из [How to Self‑Host an S3‑Compatible Object Store with MinIO on Your Staging Server (and Save Hundreds of Dollars a Month)]([https://medium.com/@kaloyanmanev/how-to-run-rabbitmq-in-docker-compose-e5baccc3e644](https://www.freecodecamp.org/news/how-to-self-host-an-s3-compatible-object-store-with-minio-on-your-staging-server/#heading-5-step-2-run-minio-with-docker-compose) 

Официальный образ больше не доступен от вендора, используя community edition pgsty/minio:RELEASE.2026-08-04T00-00-00Z. 

Поднял docker контейнер
```
student:~/S3$ cat docker-compose.staging.yml
volumes:
  minio-data:

services:
  minio:
    image: pgsty/minio:RELEASE.2026-08-04T00-00-00Z 
    container_name: minio-staging
    restart: unless-stopped
    environment:
      - MINIO_ROOT_USER=${MINIO_ROOT_USER:-admin}
      - MINIO_ROOT_PASSWORD=${MINIO_ROOT_PASSWORD:-change-me-please}
      # Tell MinIO which public domain to sign URLs with
      - MINIO_SERVER_URL=https://minio-staging.domain.com
      - MINIO_BROWSER_REDIRECT_URL=https://minio-console-staging.domain.com
    command: server /data --console-address ":9001"
    volumes:
      - minio-data:/data
    ports:
      - "9000:9000"  # S3 API
      - "9001:9001"  # Web console
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:9000/minio/health/live"]
      interval: 10s
      timeout: 5s
      retries: 3
      start_period: 30s

```

проверка статуса
```
student:~/S3$ docker compose -f docker-compose.staging.yml up -d minio
[+] up 3/3
 ✔ Network s3_default      Created                                                                                                                                                        0.6s
 ✔ Volume s3_minio-data    Created                                                                                                                                                        0.1s
 ✔ Container minio-staging Started                                                                                                                                                        2.7s
student:~/S3$ docker compose -f docker-compose.staging.yml logs -f minio
minio-staging  | INFO: Formatting 1st pool, 1 set(s), 1 drives per set.
minio-staging  | INFO: WARNING: Host local has more than 0 drives of set. A host failure will result in data becoming unavailable.
minio-staging  | MinIO Object Storage Server
minio-staging  | Copyright: 2015-2026 MinIO, Inc.
minio-staging  | License: GNU AGPLv3 - https://www.gnu.org/licenses/agpl-3.0.html
minio-staging  | Version: RELEASE.2026-08-04T00-00-00Z (go1.26.5 linux/amd64)
minio-staging  | 
minio-staging  | API: https://minio-staging.domain.com 
minio-staging  | WebUI: https://minio-console-staging.domain.com 
minio-staging  | 
minio-staging  | Docs: https://silo.pgsty.com/docs/
^C
student:~/S3$ docker ps
CONTAINER ID   IMAGE                                      COMMAND                  CREATED          STATUS                    PORTS                                                             NAMES
81b81839ef5e   pgsty/minio:RELEASE.2026-08-04T00-00-00Z   "/usr/bin/docker-ent…"   56 seconds ago   Up 55 seconds (healthy)   0.0.0.0:9000-9001->9000-9001/tcp, [::]:9000-9001->9000-9001/tcp   minio-staging
```

UI доступен по http://127.0.0.1:9001/login
<img width="1847" height="667" alt="image" src="https://github.com/user-attachments/assets/26a78a68-78e6-4b7f-ac42-e82992e62523" />





## (2) Создать бакет используя API ## 
Создал бакет используя API самого докера 
```
-- подключиться к mc серверу
student:~/S3$ docker exec -it minio-staging \
  mc alias set local http://localhost:9000 admin change-me-please
mc: Configuration written to `/tmp/.mc/config.json`. Please update your access credentials.
mc: Successfully created `/tmp/.mc/share`.
mc: Initialized share uploads `/tmp/.mc/share/uploads.json` file.
mc: Initialized share downloads `/tmp/.mc/share/downloads.json` file.
Added `local` successfully.
-- создать бакет
student:~/S3$ docker exec -it minio-staging mc mb local/domain-files-staging
Bucket created successfully `local/domain-files-staging`.
-- выбрать политику
student:~/S3$ docker exec -it minio-staging \
  mc anonymous set none local/domain-files-staging
Access permission for `local/domain-files-staging` is set to `private`
```

## (3) Сохранить файл в бакет используя API ##
Загрузил файл в бакет domain-files-staging
```
student:/data$ docker exec -it minio-staging sh -c "mc alias set local http://localhost:9000 admin change-me-please && mc cp /tmp/ll.lst local/domain-files-staging/"
Added `local` successfully.
/tmp/ll.lst:                       10 B / 10 B ┃▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓┃ 37 B/s 0sstudent:/data$ 
```

## (4) Сохраните файл программно ##

Сделал это через питон script с помощью кода
```
import io
from minio import Minio

client = Minio("localhost:9000", access_key="admin", secret_key="change-me-please", secure=False)

# Convert string data to a byte stream
file_data = b"This is the content of my in-memory file."
stream = io.BytesIO(file_data)

client.put_object(
    bucket_name="domain-files-staging",
    object_name="memory-file.txt",
    data=stream,
    length=len(file_data), # MinIO requires the size of the stream
    content_type="text/plain"
)
print("In-memory file uploaded successfully.")
```
