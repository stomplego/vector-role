# Ansible Role: Vector

[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-vector--role-blue.svg)](https://galaxy.ansible.com/stomplego/vector-role)

## Описание

Роль для установки и настройки Vector — высокопроизводительного конвейера для сбора, трансформации и маршрутизации логов и метрик.

## Требования

- Ansible >= 2.9
- Ubuntu 20.04 / 22.04 / 24.04
- Минимум 256 МБ свободного места
- Доступ в интернет для скачивания бинарных файлов

## Переменные роли

Все переменные, которые можно переопределить, хранятся в `defaults/main.yml`:

| Имя | Значение по умолчанию | Описание |
|-----|----------------------|----------|
| `vector_version` | `0.48.0` | Версия Vector для установки |
| `vector_config_dir` | `/etc/vector` | Директория конфигурации |
| `vector_data_dir` | `/var/lib/vector` | Директория для данных |
| `vector_log_dir` | `/var/log/vector` | Директория для логов |
| `vector_user` | `vector` | Системный пользователь |
| `vector_group` | `vector` | Системная группа |
| `vector_source_type` | `demo_logs` | Тип источника (`demo_logs`, `file`, `journald`) |
| `vector_source_format` | `apache_common` | Формат логов для демо-источника |
| `vector_source_interval` | `1` | Интервал генерации логов (секунды) |
| `vector_sink_type` | `console` | Тип приёмника (`console`, `http`, `prometheus_exporter`) |
| `vector_sink_target` | `stdout` | Целевой вывод для console |
| `vector_sink_encoding` | `text` | Формат кодирования (`text`, `json`) |

### Внутренние переменные (в `vars/main.yml`)

| Имя | Описание |
|-----|----------|
| `vector_download_url` | URL для скачивания бинарного файла |
| `vector_binary_path` | `/usr/local/bin/vector` |
| `vector_systemd_path` | `/etc/systemd/system/vector.service` |
| `vector_dependencies` | Список зависимых пакетов |

## Зависимости

Нет внешних зависимостей.

## Пример playbook

### Базовая установка

```yaml
---
- name: Установка Vector
  hosts: all
  become: yes
  roles:
    - role: stomplego.vector-role

### Переопределение переменных
```yaml
- hosts: log_servers
  roles:
    - role: vector-role
      vars:
        vector_source_type: file
        vector_source_file_path: /var/log/nginx/access.log
        vector_sink_type: http
        vector_sink_uri: https://logs.example.com/ingest
        vector_sink_method: post

### Запуск с метриками Prometheus
```yaml
- hosts: monitoring
  roles:
    - role: vector-role
      vars:
        vector_source_type: internal_metrics
        vector_sink_type: prometheus_exporter
        vector_sink_port: 9598
