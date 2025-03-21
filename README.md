# test-task-mindbox-kuber

Решение тестового задания для Mindbox

---

## Что делает это решение

- Запускает приложение в **двух подах** — чтобы оно было доступно, даже если один под упадёт.
- **Автоматически масштабируется** до **четырёх подов**, когда днём возрастает нагрузка.
- Использует **минимум ресурсов** ночью, когда трафик падает.
- Добавлены простые **проверки готовности и живости** приложения (probes), чтобы Kubernetes знал, когда на под можно слать трафик.

---

## Структура файлов

```
.
├── deployment.yaml      # Описание основного деплоя приложения
└── hpa.yaml             # Автоскейлинг по нагрузке на CPU
```

---

## Описание принятых решений

- **replicas: 2**  
  Минимум 2 пода, чтобы приложение оставалось доступно даже при проблемах с одним из них.

- **requests.cpu: 200m**  
  На запуске приложение требует больше ресурсов. 200m CPU позволяет ему быстрее инициализироваться.

- **limits.cpu: 500m**  
  Ограничиваем максимум, чтобы поды не забирали все ресурсы на ноде.

- **requests.memory: 128Mi / limits.memory: 256Mi**  
  По задаче приложение стабильно потребляет около 128Mi. Сделан небольшой запас.

- **HorizontalPodAutoscaler (HPA)**  
  Автоматически увеличивает количество подов при повышении загрузки CPU.  
  → minReplicas: 2 (работает всегда хотя бы 2 пода)  
  → maxReplicas: 4 (нагрузочное тестирование показало, что 4 пода хватает для пиков)

- **readinessProbe и livenessProbe**  
  Простые проверки, чтобы Kubernetes не слал запросы на неподготовленный или зависший под.

---

## Как применить

```bash
kubectl apply -f deployment.yaml
kubectl apply -f hpa.yaml
```

---
