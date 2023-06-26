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

  

  **Конфигурация logstash**

  ```Конфигурация logstash:
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

  **Конфигурация rsyslog**

  ```Конфигурация rsyslog:
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

  

  

  

  
