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


```yaml
global:
  scrape_interval: 15s
scrape_configs:
  - job_name: 'prometheus'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:9090']
  - job_name: 'pushgateway'
    scrape_interval: 5s
    static_configs:
      - targets: ['pushgateway:9091']
```

```yaml
version: '3.8'
services:
  prometheus:
    image: prom/prometheus:latest
    container_name: KadancevV-netology-prometheus
    ports:
      - "9090:9090"
    volumes:
      - prometheus-data:/prometheus
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    networks:
      - KadancevV-my-netology-hw
volumes:
  prometheus-data:
networks:
  KadancevV-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
```

---

### Задание 4

`Создана конфигурация `docker-compose` для Pushgateway с именем контейнера `KadancevV-netology-pushgateway`. Обеспечен внешний доступ к порту `9091`.`

```yaml
version: '3.8'
services:
  prometheus:
    image: prom/prometheus:latest
    container_name: KadancevV-netology-prometheus
    ports:
      - "9090:9090"
    volumes:
      - prometheus-data:/prometheus
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    networks:
      - KadancevV-my-netology-hw
  pushgateway:
    image: prom/pushgateway:latest
    container_name: KadancevV-netology-pushgateway
    ports:
      - "9091:9091"
    networks:
      - KadancevV-my-netology-hw
volumes:
  prometheus-data:
networks:
  KadancevV-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
```

---

### Задание 5

Создана конфигурация `docker-compose` для Grafana с именем контейнера `KadancevV-netology-grafana`. Добавлены тома для данных (`grafana-data`) и конфигурации (`custom.ini`), настроена переменная окружения для пути к конфигурации. В `custom.ini` указаны логин `KadancevV` и пароль `netology`. Обеспечен внешний доступ к порту `3000` через порт `80`.

```ini
[auth]
disable_login_form = false

[auth.basic]
enabled = true

[auth.anonymous]
enabled = false

[security]
admin_user = KadancevV
admin_password = netology
```


```yaml
version: '3.8'
services:
  prometheus:
    image: prom/prometheus:latest
    container_name: KadancevV-netology-prometheus
    ports:
      - "9090:9090"
    volumes:
      - prometheus-data:/prometheus
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    networks:
      - KadancevV-my-netology-hw
  pushgateway:
    image: prom/pushgateway:latest
    container_name: KadancevV-netology-pushgateway
    ports:
      - "9091:9091"
    networks:
      - KadancevV-my-netology-hw
  grafana:
    image: grafana/grafana:latest
    container_name: KadancevV-netology-grafana
    ports:
      - "80:3000"
    volumes:
      - grafana-data:/var/lib/grafana
      - ./custom.ini:/etc/grafana/grafana.ini
    environment:
      - GF_PATHS_CONFIG=/etc/grafana/grafana.ini
    networks:
      - KadancevV-my-netology-hw
volumes:
  prometheus-data:
  grafana-data:
networks:
  KadancevV-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
```

---

### Задание 6

`Настроена поочередность запуска контейнеров: Pushgateway → Prometheus → Grafana. Добавлены режимы перезапуска `always` для всех контейнеров. Все контейнеры используют сеть `KadancevV-my-netology-hw`. Сценарий запущен в detached-режиме с помощью `docker compose up -d`.`

---

### Задание 7

Выполнен запрос для помещения метрики `KadancevV` со значением 5 в Pushgateway. В Grafana выполнен вход с логином `KadancevV` и паролем `netology`. Создан Data Source Prometheus с URL `http://prometheus:9090`. Построен график на основе метрики `KadancevV`.

```yaml
version: '3.8'
services:
  pushgateway:
    image: prom/pushgateway:latest
    container_name: KadancevV-netology-pushgateway
    ports:
      - "9091:9091"
    restart: always
    networks:
      - KadancevV-my-netology-hw
  prometheus:
    image: prom/prometheus:latest
    container_name: KadancevV-netology-prometheus
    ports:
      - "9090:9090"
    volumes:
      - prometheus-data:/prometheus
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    restart: always
    depends_on:
      - pushgateway
    networks:
      - KadancevV-my-netology-hw
  grafana:
    image: grafana/grafana:latest
    container_name: KadancevV-netology-grafana
    ports:
      - "80:3000"
    volumes:
      - grafana-data:/var/lib/grafana
      - ./custom.ini:/etc/grafana/grafana.ini
    environment:
      - GF_PATHS_CONFIG=/etc/grafana/grafana.ini
    restart: always
    depends_on:
      - prometheus
    networks:
      - KadancevV-my-netology-hw
volumes:
  prometheus-data:
  grafana-data:
networks:
  KadancevV-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
```

![Скриншот команды docker ps](https://github.com/victorialugi/docker2-homework/blob/main/task7_docker_ps.png)

![Скриншот графика LugininaV](https://github.com/victorialugi/docker2-homework/blob/main/task7_grafana.png)

---

### Задание 8

Остановлены и удалены все контейнеры одной командой: `docker container rm -f $(docker container ls -aq)`.

![Скриншот удаления контейнеров](https://github.com/victorialugi/docker2-homework/blob/main/task8_containers_removed.png)

---

### Задание 9

Создана конфигурация `docker-compose` для Alertmanager с именем контейнера `LugininaV-netology-alertmanager`. Настроены тома, сеть, режим перезапуска и очередность запуска. Обновлена конфигурация Prometheus для интеграции с Alertmanager, добавлены правила для генерации алерта. Для теста использовала `docker stop LugininaV-netology-prometheus`.

![Скриншот работы Alertmanager](https://github.com/victorialugi/docker2-homework/blob/main/task9_alertmanager.png)

```yaml
version: '3.8'
services:
  pushgateway:
    image: prom/pushgateway:latest
    container_name: LugininaV-netology-pushgateway
    ports:
      - "9091:9091"
    restart: always
    networks:
      - LugininaV-my-netology-hw
  prometheus:
    image: prom/prometheus:latest
    container_name: LugininaV-netology-prometheus
    ports:
      - "9090:9090"
    volumes:
      - prometheus-data:/prometheus
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - ./rules.yml:/etc/prometheus/rules.yml
    restart: always
    depends_on:
      - pushgateway
    networks:
      - LugininaV-my-netology-hw
  grafana:
    image: grafana/grafana:latest
    container_name: LugininaV-netology-grafana
    ports:
      - "80:3000"
    volumes:
      - grafana-data:/var/lib/grafana
      - ./custom.ini:/etc/grafana/grafana.ini
    environment:
      - GF_PATHS_CONFIG=/etc/grafana/grafana.ini
    restart: always
    depends_on:
      - prometheus
    networks:
      - LugininaV-my-netology-hw
  alertmanager:
    image: prom/alertmanager:latest
    container_name: LugininaV-netology-alertmanager
    ports:
      - "9093:9093"
    volumes:
      - alertmanager-data:/alertmanager
      - ./alertmanager.yml:/etc/alertmanager/alertmanager.yml
    restart: always
    depends_on:
      - prometheus
    networks:
      - LugininaV-my-netology-hw
volumes:
  prometheus-data:
  grafana-data:
  alertmanager-data:
networks:
  LugininaV-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
```

```

`При необходимости прикрепитe сюда скриншоты
![Название скриншота](ссылка на скриншот)`
