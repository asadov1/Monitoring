# ДЗ_1. Установка и настройка Prometheus, использование exporters, настройка алертов

**Цели:**

- Установить и настроить Prometheus.
- Результатом выполнения данного дз будет являться публичный репозиторий в системе контроля версий (Github, Gitlab, etc.) в котором будет находится Readme с описание выполненых действий.
- Файлы конфигурации prometheus и alertmanager должны находится в директории GAP-1.

**Выполненые действия:**

- Настроен стек GAP.

- Настроен CMS Wordpress.

- Сервисы развернуты в Docker-Compose.

- Настроены экспортеры для всего, что можно, как и написано в задании.

- Файл docker-compose.yml в корневой папке содержит все приложения GAP и некоторые экпортеры (blackbox_exporter, node-exporter, также для мониторинга
  Docker разевернул приложения cadvisor и redis).

- В alertmanager файл alert.rules настроил алерты с провекой доступности сервисов - severity: critical.

- В wordpress-docker еще один docker-compose.yml в котором настроены приложения cms, nginx, phpmyadmin, mysql и экспортеры связаные с этим.

- Также настроил мониторинг всех сервисов, ОС и Docker  в дашбордах Grafana, но об этом видимо в слещующий раз.



# ДЗ_2. Формирование dashboard на основе собранных данных с Grafana

**Цели:**

- Сформировать dashboard на основе собранных данных с Grafana



**Выполненые действия:**


- В паппку infra добавлен дашборды для мониториншга Docker контейнеров [Docker]( http://62.173.152.239:3000/d/4dMaCsRZz/docker-container-and-host-metrics?orgId=1&refresh=10s "Grafana Docker Dashboard") и самой ОС через node exporter [Node exporter](http://62.173.152.239:3000/d/rYdddlPWk/node-exporter-full?orgId=1 "Node exporter dashboard")

- В папку app добавлены аборты для мониторинга [MySQL](http://62.173.152.239:3000/d/MQWgroiiz/mysql-overview?orgId=1&refresh=1m "Grafana MySQL dashboard"), [Nginx](http://62.173.152.239:3000/d/MQWgroiiz/mysql-overview?orgId=1&refresh=1m "Grafana Nginx dashboard") и [BlackBox Exprter](http://62.173.152.239:3000/d/xtkCtBkiz/prometheus-blackbox-exporter?orgId=1&refresh=10s "Grafana CMS dashboard") для мониторинга доступности CMS WordPress.

- **Добавил Alert для проверки доступности Wordpress и отправку уведомления в telegramm**

  

# ДЗ_3. Установка ELK

**Цели:**

- Установить ELK

  

**Выполненые действия:**


- Установлен ELK стек в Doker

- Настроен rsyslog и logstash

- Добавлен index ***sshd-logs-**

- Сделал визуализации sshd в кибана

  

**Созданые индексы elasticsearch:**

```Созданые индексы elasticsearch
[root@localhost prometheus_stack]# curl -u elastic:changeme -X GET "localhost:9200/_cat/indices?v=true"
health status index                                                        uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   metrics-endpoint.metadata_current_default                    ijb-7-S5Tw-SrAyXpO0_Gw   1   0          0            0       247b           247b
green  open   .internal.alerts-observability.metrics.alerts-default-000001 -ZjtwqQhRn6bORfzHLwCZg   1   0          0            0       247b           247b
yellow open   sshd-logs-2023.06.22                                         rtCCPNb_Qu2uuYWhm54wAQ   1   1       1390            0    803.7kb        803.7kb
green  open   .internal.alerts-observability.logs.alerts-default-000001    JZS9su-wSX-1wKpWDB5J_A   1   0          0            0       247b           247b
green  open   .internal.alerts-observability.uptime.alerts-default-000001  Vg7XkH3_RNmdNCqU6NJKvg   1   0          0            0       247b           247b
green  open   .fleet-files-endpoint-000001                                 tpyP1aTHQIq6egUtLupEXQ   1   0          0            0       247b           247b
green  open   .fleet-file-data-agent-000001                                0f7v0DMOTAKVj91rcRduBg   1   0          0            0       247b           247b
green  open   .fleet-files-agent-000001                                    hTCgIoc6RkqItVWOxAyOTg   1   0          0            0       247b           247b
green  open   .fleet-file-data-endpoint-000001                             6kuJNMEnSZ2U5MdDMjZ9IA   1   0          0            0       247b           247b
green  open   .internal.alerts-security.alerts-default-000001              deTgJERWS6OA2YLN3TA5TQ   1   0          0            0       247b           247b
green  open   .internal.alerts-observability.slo.alerts-default-000001     IVpp8CvfTEWSyVIoxS5oaw   1   0          0            0       247b           247b
green  open   .internal.alerts-observability.apm.alerts-default-000001     tfRl_IMbQsWC9DLAgfCExA   1   0          0            0       247b           247b
```



**Конфигурация logstash:**

```Конфигурация logstash
[root@localhost prometheus_stack]# cat /opt/prometheus_stack/docker-elk/logstash/config/logstash.yml 
---
## Default Logstash configuration from Logstash base image.
## https://github.com/elastic/logstash/blob/main/docker/data/logstash/config/logstash-full.yml
#
http.host: 0.0.0.0

node.name: logstash
[root@localhost prometheus_stack]# 
[root@localhost prometheus_stack]# 
[root@localhost prometheus_stack]# 
[root@localhost prometheus_stack]# 
[root@localhost prometheus_stack]# cat /opt/prometheus_stack/docker-elk/logstash/pipeline/logstash.conf 
input {
  tcp {
    port => 5044
  }
}

filter {
}

output {
  elasticsearch {
    hosts => ["elasticsearch:9200"]
    user => "elastic"
    password => "changeme"
    index => "sshd-logs-%{+YYYY.MM.dd}"
  }
}
```

**Конфигурация rsyslog:**

```Конфигурация rsyslog
[root@localhost prometheus_stack]# cat /etc/rsyslog.conf 
# rsyslog configuration file

# For more information see /usr/share/doc/rsyslog-*/rsyslog_conf.html
# or latest version online at http://www.rsyslog.com/doc/rsyslog_conf.html 
# If you experience problems, see http://www.rsyslog.com/doc/troubleshoot.html

#### GLOBAL DIRECTIVES ####

# Where to place auxiliary files
global(workDirectory="/var/lib/rsyslog")

# Use default timestamp format
module(load="builtin:omfile" Template="RSYSLOG_TraditionalFileFormat")

#### MODULES ####

module(load="imuxsock"    # provides support for local system logging (e.g. via logger command)
       SysSock.Use="off") # Turn off message reception via local log socket; 
                          # local messages are retrieved through imjournal now.
module(load="imjournal"             # provides access to the systemd journal
       UsePid="system" # PID nummber is retrieved as the ID of the process the journal entry originates from
       StateFile="imjournal.state") # File to store the position in the journal
#module(load="imklog") # reads kernel messages (the same are read from journald)
#module(load="immark") # provides --MARK-- message capability

# Include all config files in /etc/rsyslog.d/
include(file="/etc/rsyslog.d/*.conf" mode="optional")

# Provides UDP syslog reception
# for parameters see http://www.rsyslog.com/doc/imudp.html
#module(load="imudp") # needs to be done just once
#input(type="imudp" port="514")

# Provides TCP syslog reception
# for parameters see http://www.rsyslog.com/doc/imtcp.html
#module(load="imtcp") # needs to be done just once
#input(type="imtcp" port="514")

#### RULES ####

# Log all kernel messages to the console.
# Logging much else clutters up the screen.
#kern.*                                                 /dev/console

# Log anything (except mail) of level info or higher.
# Don't log private authentication messages!
*.info;mail.none;authpriv.none;cron.none                /var/log/messages

# The authpriv file has restricted access.
authpriv.*                                              /var/log/secure

# Log all the mail messages in one place.
mail.*                                                  -/var/log/maillog


# Log cron stuff
cron.*                                                  /var/log/cron

# Everybody gets emergency messages
*.emerg                                                 :omusrmsg:*

# Save news errors of level crit and higher in a special file.
uucp,news.crit                                          /var/log/spooler

# Save boot messages also to boot.log
local7.*                                                /var/log/boot.log


# ### sample forwarding rule ###
#action(type="omfwd"  
# # An on-disk queue is created for this action. If the remote host is
# # down, messages are spooled to disk and sent when it is up again.
#queue.filename="fwdRule1"       # unique name prefix for spool files
#queue.maxdiskspace="1g"         # 1gb space limit (use as much as possible)
#queue.saveonshutdown="on"       # save messages to disk on shutdown
#queue.type="LinkedList"         # run asynchronously
#action.resumeRetryCount="-1"    # infinite retries if host is down
# # Remote Logging (we use TCP for reliable delivery)
# # remote_host is: name/ip, e.g. 192.168.0.1, port optional e.g. 10514
#Target="remote_host" Port="XXX" Protocol="tcp")
*.* @@172.30.0.9:5044
```



**Результат вывода данных в индексе sshd:**

<img src="https://raw.githubusercontent.com/asadov1/Monitoring/main/files/image1.png" lt="net_diag" style="zoom:80%;" /> 



**Визуализация в Kibana по sshd count@timestamp**:

<img src="https://raw.githubusercontent.com/asadov1/Monitoring/main/files/image2.png" lt="net_diag" style="zoom:80%;" />



# ДЗ_4. Установка beats



**Цели**:

- Установить beats

**Выполненые действия:**

- Установлены heartbeat, metricbeat и filebeat
- Добавлены dashboards в Kibana для каждого из установленных битов
- Добавлены индексы
- Посмотрел визуализации для Docker, check status итд
- Для каждого из битов создал пользователя для подключения к elastic

**Созданые индексы elasticsearch:**

```
[root@localhost docker-elk]# curl -u elastic:ciscocisco -X GET "localhost:9200/_cat/indices?v=true"
health status index                                                        uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   .ds-heartbeat-8.8.1-2023.06.26-000001                        UIhjDkAHQ3K0qMmZcwgx4Q   1   1     128904            0    135.4mb        135.4mb
green  open   .internal.alerts-observability.logs.alerts-default-000001    JZS9su-wSX-1wKpWDB5J_A   1   0          0            0       248b           248b
green  open   .internal.alerts-observability.uptime.alerts-default-000001  Vg7XkH3_RNmdNCqU6NJKvg   1   0          0            0       248b           248b
green  open   .ds-.monitoring-kibana-8-mb-2023.06.28-000001                zScNF2AzTiCOXFUNogDsuQ   1   0       2999            0      1.4mb          1.4mb
green  open   .fleet-file-data-agent-000001                                0f7v0DMOTAKVj91rcRduBg   1   0          0            0       248b           248b
green  open   .fleet-files-agent-000001                                    hTCgIoc6RkqItVWOxAyOTg   1   0          0            0       248b           248b
green  open   .internal.alerts-observability.slo.alerts-default-000001     IVpp8CvfTEWSyVIoxS5oaw   1   0          0            0       248b           248b
green  open   .ds-.monitoring-logstash-8-mb-2023.06.27-000001              eAjjO1FuTpixEaM0mYXl3w   1   0      70534            0     20.2mb         20.2mb
green  open   .internal.alerts-observability.apm.alerts-default-000001     tfRl_IMbQsWC9DLAgfCExA   1   0          0            0       248b           248b
green  open   .internal.alerts-observability.metrics.alerts-default-000001 -ZjtwqQhRn6bORfzHLwCZg   1   0          0            0       248b           248b
yellow open   .ds-filebeat-8.8.1-2023.06.28-000001                         8KXD-P2RTMel54UnWzAaaw   1   1      35964            0      5.7mb          5.7mb
green  open   .fleet-files-endpoint-000001                                 tpyP1aTHQIq6egUtLupEXQ   1   0          0            0       247b           247b
yellow open   sshd-logs-2023.06.28                                         CGoW1224QLGZkH0RQKf3Ug   1   1       2407            0      1.1mb          1.1mb
green  open   .fleet-file-data-endpoint-000001                             6kuJNMEnSZ2U5MdDMjZ9IA   1   0          0            0       247b           247b
green  open   .internal.alerts-security.alerts-default-000001              deTgJERWS6OA2YLN3TA5TQ   1   0          0            0       248b           248b
green  open   .monitoring-beats-7-2023.06.28                               sgGmd3QYSu21tEwxEncAtA   1   0       7286            0      7.9mb          7.9mb
yellow open   .ds-metricbeat-8.8.1-2023.06.27-000001                       4CdREcWYQvadQV3fdadbbA   1   1    1319071            0   1011.6mb       1011.6mb
yellow open   sshd-logs-2023.06.27                                         a0JNNOe-SeWwqwMMBqoJWQ   1   1       2898            0      1.2mb          1.2mb
```

**Конфигурация heartbeat:**

```
[root@localhost config]# cat heartbeat.yml 
## Heartbeat configuration
## https://github.com/elastic/beats/blob/main/deploy/docker/heartbeat.docker.yml
#

name: heartbeat

heartbeat.monitors:
- type: http
  schedule: '@every 5s'
  urls:
    - http://elasticsearch:9200
  username: heartbeat_internal
  password: ${HEARTBEAT_INTERNAL_PASSWORD}

- type: icmp
  schedule: '@every 5s'
  hosts:
    - elasticsearch

- type: http
  schedule: '@every 5s'
  urls: ["http://otus.ru", "http://google.com"]

# output.logstash:
#   hosts: ["logstash:5044"]

processors:
- add_cloud_metadata: ~

monitoring:
  enabled: true
  elasticsearch:
    username: beats_system
    password: ${BEATS_SYSTEM_PASSWORD}

output.elasticsearch:
  hosts: [ http://elasticsearch:9200 ]
  username: heartbeat_internal
  password: ${HEARTBEAT_INTERNAL_PASSWORD}

## HTTP endpoint for health checking
## https://www.elastic.co/guide/en/beats/heartbeat/current/http-endpoint.html
#

http:
  enabled: true
  host: 0.0.0.0
```

**Конфигурация filebeat:**

```
[root@localhost extensions]# cat filebeat/config/filebeat.yml 
## Filebeat configuration
## https://github.com/elastic/beats/blob/main/deploy/docker/filebeat.docker.yml
#

name: filebeat

filebeat.config:
  modules:
    path: ${path.config}/modules.d/*.yml
    reload.enabled: false

filebeat.autodiscover:
  providers:
    # The Docker autodiscover provider automatically retrieves logs from Docker
    # containers as they start and stop.
    - type: docker
      hints.enabled: true

setup.kibana:
  host: "http://kibana:5601"        

processors:
  - add_cloud_metadata: ~

monitoring:
  enabled: true
  elasticsearch:
    username: beats_system
    password: ${BEATS_SYSTEM_PASSWORD}

output.elasticsearch:
  hosts: [ http://elasticsearch:9200 ]
  username: filebeat_internal
  password: ${FILEBEAT_INTERNAL_PASSWORD}

filebeat.inputs:
 - type: log
   enabled: true
   paths:
     - /var/log/secure*
   document_type: syslog
   ignore_older: 24h
   close_inactive: 1h
   clean_inactive: 48h

filebeat.modules:
 - module: system
   # Syslog
   syslog:
     enabled: true
     # Set custom paths for the log files. If left empty,
     # Filebeat will choose the paths depending on your OS.
     var.paths: ["/var/log/auth.log*"]
  
   # Authorization logs
   auth:
     enabled: true
     # Set custom paths for the log files. If left empty,
     # Filebeat will choose the paths depending on your OS.
     var.paths: ["/var/log/secure*"]

## HTTP endpoint for health checking
## https://www.elastic.co/guide/en/beats/filebeat/current/http-endpoint.html
#

http:
  enabled: true
  host: 0.0.0.0
```

**Конфигурация metricbeat:**

```
[root@localhost extensions]# cat metricbeat/config/metricbeat.yml 
## Metricbeat configuration
## https://github.com/elastic/beats/blob/main/deploy/docker/metricbeat.docker.yml
#

name: metricbeat

metricbeat.config:
  modules:
    path: ${path.config}/modules.d/*.yml
    # Reload module configs as they change:
    reload.enabled: false

metricbeat.autodiscover:
  providers:
    - type: docker
      hints.enabled: true

metricbeat.modules:
- module: elasticsearch
  hosts: [ http://elasticsearch:9200 ]
  username: monitoring_internal
  password: ${MONITORING_INTERNAL_PASSWORD}
  xpack.enabled: true
  period: 10s
  enabled: true
- module: logstash
  hosts: [ http://logstash:9600 ]
  xpack.enabled: true
  period: 10s
  enabled: true
- module: kibana
  hosts: [ http://kibana:5601 ]
  username: monitoring_internal
  password: ${MONITORING_INTERNAL_PASSWORD}
  xpack.enabled: true
  period: 10s
  enabled: true
- module: system
  period: 10s
  metricsets:
    - cpu
    - memory
  enabled: true    
- module: docker
  metricsets:
    - container
    - cpu
    - diskio
    - healthcheck
    - info
    #- image
    - memory
    - network
  hosts: [ unix:///var/run/docker.sock ]
  period: 10s
  enabled: true

processors:
  - add_cloud_metadata: ~

monitoring:
  enabled: true
  elasticsearch:
    username: beats_system
    password: ${BEATS_SYSTEM_PASSWORD}

output.elasticsearch:
  hosts: [ http://elasticsearch:9200 ]
  username: metricbeat_internal
  password: ${METRICBEAT_INTERNAL_PASSWORD}

setup.kibana:
  host: "kibana:5601"

## HTTP endpoint for health checking
## https://www.elastic.co/guide/en/beats/metricbeat/current/http-endpoint.html
#

http:
  enabled: true
  host: 0.0.0.0
```



**Визуализации heartbeat:**

<img src="https://raw.githubusercontent.com/asadov1/Monitoring/main/files/heart1.png" lt="net_diag" style="zoom:80%;" />

<img src="https://raw.githubusercontent.com/asadov1/Monitoring/main/files/heart2.png" lt="net_diag" style="zoom:80%;" />

<img src="https://raw.githubusercontent.com/asadov1/Monitoring/main/files/heart3.png" lt="net_diag" style="zoom:80%;" />

**Визуализации metricbeat:**

<img src="https://raw.githubusercontent.com/asadov1/Monitoring/main/files/metric_docker.png" lt="net_diag" style="zoom:80%;" />

<img src="https://raw.githubusercontent.com/asadov1/Monitoring/main/files/metric_host.png" lt="net_diag" style="zoom:80%;" />

<img src="https://raw.githubusercontent.com/asadov1/Monitoring/main/files/metric_discover.png" lt="net_diag" style="zoom:80%;" />

**Визуализация filebeat:**

<img src="https://raw.githubusercontent.com/asadov1/Monitoring/main/files/filebeat_1.png" lt="net_diag" style="zoom:80%;" />
