# 📊 Мониторинг с Prometheus и Grafana

Этот проект предоставляет готовую инфраструктуру для мониторинга систем и сервисов с использованием **Prometheus** в качестве системы сбора метрик и **Grafana** — для визуализации.

---

## 🏗️ Архитектура мониторинга

Архитектура построена по классической модели **pull-based** (опрос с интервалом):

1. **Экспортеры** (node_exporter, cadvisor и др.) собирают метрики с целевых хостов или сервисов.
2. **Prometheus** периодически опрашивает эти эндпоинты (`/metrics`) и сохраняет данные временных рядов.
3. **Grafana** подключается к Prometheus как к источнику данных и отображает дашборды в реальном времени.

<div align="center">
  <img src="https://prometheus.io/assets/architecture.png" alt="Prometheus Architecture" width="600"/>
</div>

> 💡 **Преимущество**: полная прозрачность, гибкая настройка алертов и мощная система запросов (PromQL).

---

## 📈 Основные метрики

Вот ключевые метрики, которые рекомендуется отслеживать:

| Метрика                     | Описание                                               | Пример использования                     |
|----------------------------|--------------------------------------------------------|------------------------------------------|
| `up`                       | Доступность целевого эндпоинта                         | `up{job="node_exporter"} == 0` → Алерт  |
| `node_cpu_seconds_total`   | Статистика CPU по режимам (idle, user, system и др.)  | `% использования CPU`                    |
| `node_memory_MemAvailable_bytes` | Доступная память на хосте                            | Тревога при < 1 ГБ                       |
| `node_disk_io_time_seconds_total` | Время ввода-вывода на диск                          | Выявление I/O bottleneck                 |
| `container_cpu_usage_seconds_total` | Использование CPU контейнерами (если используется cAdvisor) | Мониторинг микросервисов            |

<font color="#2E8B57">Зелёный текст</font> — норма, <font color="#DC143C">красный</font> — критическое состояние.

---

## 🛠️ Пример конфигурационного файла Prometheus (`prometheus.yml`)

```yaml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node_exporter'
    static_configs:
      - targets: ['localhost:9100']

  - job_name: 'cadvisor'
    static_configs:
      - targets: ['localhost:8080']
