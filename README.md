# Домашнее задание к занятию "`Docker, часть 2`" - `Kadancev Vladimir`


### Инструкция по выполнению домашнего задания

   1. Сделайте `fork` данного репозитория к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/git-hw или  https://github.com/имя-вашего-репозитория/7-1-ansible-hw).
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.
   
Желаем успехов в выполнении домашнего задания!
   
### Дополнительные материалы, которые могут быть полезны для выполнения задания

1. [Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

---

### Задание 1

`Docker Compose — это инструмент для управления много-контейнерными приложениями, который позволяет описать их конфигурацию в одном YAML-файле и запускать одной командой, упрощая настройку связей между сервисами, такими как веб-сервер и база данных. Он может улучшить мою жизнь, так как экономит время на настройке окружений для учёбы и проектов, автоматизируя запуск сложных приложений, что особенно полезно при изучении Docker и DevOps, позволяя сосредоточиться на задачах, а не на ручной конфигурации.`

---

### Задание 2

```yaml
version: '3.8'
services: {}
volumes: {}
networks:
  KadancevV-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
```

---

### Задание 3

`Создана конфигурация `docker-compose` для Prometheus с именем контейнера `KadancevV-netology-prometheus`. Добавлены тома для данных (`prometheus-data`) и конфигурации (`prometheus.yml`), обеспечен внешний доступ к порту `9090`.`


```global:
  scrape_interval: 15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node-exporter'
    static_configs:
      - targets: ['node-exporter:9100']

  - job_name: 'pushgateway'
    honor_labels: true
    static_configs:
      - targets: ['pushgateway:9091']
```

```prometheus:
    image: prom/prometheus:latest
    container_name: KadancevV-netology-prometheus
    networks:
      KadancevV-my-netology-hw:
        ipv4_address: 10.5.0.30
    restart: unless-stopped
    ports:
      - "9090:9090"
    volumes:
      - prometheus_data:/prometheus
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/etc/prometheus/console_libraries'
      - '--web.console.templates=/etc/prometheus/consoles'
      - '--web.enable-lifecycle'
    depends_on:
      - node-exporter
      - pushgateway
```

---

### Задание 4

`Создана конфигурация `docker-compose` для Pushgateway с именем контейнера `KadancevV-netology-pushgateway`. Обеспечен внешний доступ к порту `9091`.`

```pushgateway:
    image: prom/pushgateway:latest
    container_name: KadancevV-netology-pushgateway
    networks:
      KadancevV-my-netology-hw:
        ipv4_address: 10.5.0.20
    restart: unless-stopped
    ports:
      - "9091:9091"
```

---

### Задание 5

Создана конфигурация `docker-compose` для Grafana с именем контейнера `KadancevV-netology-grafana`. Добавлены тома для данных (`grafana-data`) и конфигурации (`custom.ini`), настроена переменная окружения для пути к конфигурации. В `custom.ini` указаны логин `KadancevV` и пароль `netology`. Обеспечен внешний доступ к порту `3000` через порт `80`.

```[auth.basic]
enabled = true

[auth.anonymous]
enabled = false

[security]
admin_user = KadancevV
admin_password = netology

[server]
http_port = 3000
domain = 51.250.76.237
root_url = http://51.250.76.237:3000/
serve_from_sub_path = false

[database]
log_queries = 

[users]
allow_sign_up = false
auto_assign_org = true
auto_assign_org_role = Viewer

[auth]
disable_login_form = false
disable_signout_menu = false

[log]
mode = console
level = info
```


```grafana:
    image: grafana/grafana:latest
    container_name: KadancevV-netology-grafana
    networks:
      KadancevV-my-netology-hw:
        ipv4_address: 10.5.0.50
    restart: unless-stopped
    ports:
      - "3000:3000"
      - "80:3000"
    environment:
      - GF_PATHS_CONFIG=/etc/grafana/custom.ini
    volumes:
      - grafana_data:/var/lib/grafana
      - ./custom.ini:/etc/grafana/custom.ini
    depends_on:
      - prometheus
```

---

### Задание 6

`Настроена поочередность запуска контейнеров: Pushgateway → Prometheus → Grafana. Добавлены режимы перезапуска `always` для всех контейнеров. Все контейнеры используют сеть `KadancevV-my-netology-hw`. Сценарий запущен в detached-режиме с помощью `docker compose up -d`.`

---

### Задание 7

Выполнен запрос для помещения метрики `KadancevV` со значением 5 в Pushgateway. В Grafana выполнен вход с логином `KadancevV` и паролем `netology`. Создан Data Source Prometheus с URL `http://prometheus:9090`. Построен график на основе метрики `KadancevV`.

```pushgateway:
    image: prom/pushgateway:latest
    container_name: KadancevV-netology-pushgateway
    networks:
      KadancevV-my-netology-hw:
        ipv4_address: 10.5.0.20
    restart: unless-stopped
    ports:
      - "9091:9091"
```

![Скриншот команды docker ps](https://github.com/valdemar-2502/6.4.-Docker/blob/main/docker_ps1.png)
![Скриншот команды docker ps2](https://github.com/valdemar-2502/6.4.-Docker/blob/main/docker_ps2.png)
![Скриншот команды docker ps3](https://github.com/valdemar-2502/6.4.-Docker/blob/main/docker_ps3.png)
![Скриншот графика KadancevV](https://github.com/victorialugi/docker2-homework/blob/main/task7_grafana.png)

---

### Задание 8

Остановлены и удалены все контейнеры одной командой: `docker container rm -f $(docker container ls -aq)`.

![Скриншот удаления контейнеров](https://github.com/victorialugi/docker2-homework/blob/main/task8_containers_removed.png)

---

---

### Задание 9

Создана конфигурация `docker-compose` для Alertmanager с именем контейнера `KadancevV-netology-alertmanager`. Настроены тома, сеть, режим перезапуска и очередность запуска. Обновлена конфигурация Prometheus для интеграции с Alertmanager, добавлены правила для генерации алерта. Для теста использовал `docker stop KadancevV-netology-prometheus`.

![Скриншот работы Alertmanager](https://github.com/victorialugi/docker2-homework/blob/main/task9_alertmanager.png)

```route:
  group_by: ['alertname']
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 1h
  receiver: 'web.hook'

receivers:
  - name: 'web.hook'
    webhook_configs:
      - url: 'http://127.0.0.1:5001/'

inhibit_rules:
  - source_match:
      severity: 'critical'
    target_match:
      severity: 'warning'
    equal: ['alertname', 'dev', 'instance']
```
```version: '3.8'

networks:
  KadancevV-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16

volumes:
  prometheus_data: {}
  grafana_data: {}

services:
  node-exporter:
    image: prom/node-exporter:latest
    container_name: node-exporter
    networks:
      KadancevV-my-netology-hw:
        ipv4_address: 10.5.0.10
    restart: unless-stopped
    ports:
      - "9100:9100"

  pushgateway:
    image: prom/pushgateway:latest
    container_name: KadancevV-netology-pushgateway
    networks:
      KadancevV-my-netology-hw:
        ipv4_address: 10.5.0.20
    restart: unless-stopped
    ports:
      - "9091:9091"

  prometheus:
    image: prom/prometheus:latest
    container_name: KadancevV-netology-prometheus
    networks:
      KadancevV-my-netology-hw:
        ipv4_address: 10.5.0.30
    restart: unless-stopped
    ports:
      - "9090:9090"
    volumes:
      - prometheus_data:/prometheus
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/etc/prometheus/console_libraries'
      - '--web.console.templates=/etc/prometheus/consoles'
      - '--web.enable-lifecycle'
    depends_on:
      - node-exporter
      - pushgateway

  alertmanager:
    image: prom/alertmanager:latest
    container_name: alertmanager
    networks:
      KadancevV-my-netology-hw:
        ipv4_address: 10.5.0.40
    restart: unless-stopped
    ports:
      - "9093:9093"
    volumes:
      - ./alertmanager.yml:/etc/alertmanager/alertmanager.yml
    depends_on:
      - prometheus

  grafana:
    image: grafana/grafana:latest
    container_name: KadancevV-netology-grafana
    networks:
      KadancevV-my-netology-hw:
        ipv4_address: 10.5.0.50
    restart: unless-stopped
    ports:
      - "3000:3000"
      - "80:3000"
    environment:
      - GF_PATHS_CONFIG=/etc/grafana/custom.ini
    volumes:
      - grafana_data:/var/lib/grafana
      - ./custom.ini:/etc/grafana/custom.ini
    depends_on:
      - prometheus
```

