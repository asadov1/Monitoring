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

  
