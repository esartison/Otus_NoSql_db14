# Домашнее задание Сартисона Евгения N13 #

Описание/Пошаговая инструкция выполнения домашнего задания:
(1) Запустить сервис S3 в облаке или локально (Docker + minio)
(2) Создать бакет используя API
(3) Сохранить файл в бакет используя API
(4) Сохраните файл программно - выберите знакомый язык программирования (C#, Java, Go, Python или любой другой, для которого есть библиотека для работы с S3), отправьте файл в бакет и потом получите массив файлов из бакет и отобазите в бараузере или консоли.
Для пунктов 2 и 3 сделайте скриншоты результата выполнения.
Для пункта 4 приложите ссылку на репозитарий на гитхабе с исходным кодом.


## (1) Запустить сервис S3 в облаке или локально (Docker + minio) ## 

Использую пример из [How to Self‑Host an S3‑Compatible Object Store with MinIO on Your Staging Server (and Save Hundreds of Dollars a Month)]([https://medium.com/@kaloyanmanev/how-to-run-rabbitmq-in-docker-compose-e5baccc3e644](https://www.freecodecamp.org/news/how-to-self-host-an-s3-compatible-object-store-with-minio-on-your-staging-server/#heading-5-step-2-run-minio-with-docker-compose) 

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
<img width="1185" height="741" alt="image" src="https://github.com/user-attachments/assets/5acdb347-9752-4172-aae8-aee40d7a66c8" />




## (2) Создать бакет используя API ## 



## (3) Сохранить файл в бакет используя API ##


## (4) Сохраните файл программно ##

прочитал сообщения через CURL
```
student:~$ curl -u kalo:kalo \
  -H "Content-Type: application/json" \
  -X POST \
  -d '{"count":2, "ackmode":"ack_requeue_true", "encoding":"auto"}' \
  http://localhost:15672/api/queues/%2F/queue-any/get
[{"payload_bytes":19,"redelivered":true,"exchange":"","routing_key":"queue-any","message_count":1,"properties":{"delivery_mode":2,"headers":{}},"payload":"testing 1st message","payload_encoding":"string"},{"payload_bytes":19,"redelivered":true,"exchange":"","routing_key":"queue-any","message_count":0,"properties":{"delivery_mode":2,"headers":{}},"payload":"testing 2nd message","payload_encoding":"string"}]
```
