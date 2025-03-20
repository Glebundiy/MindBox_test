# MindBox_test
Тестовое задание Mindbox на позицию SRE
Некоторые пояснения к коду:

Окончательное количество подов обусловленно тем, что пиковая нагрузка обрабатывается 4 подами, а ночью нагрузка минимальная и достаточно одного пода.

Так как приложение потребляет больше ресурсов на старте(до 1 CPU), затем стабилизируется(0.1 CPU) были выбраны лимиты: 1 CPU и 128Mi памяти, чтобы избежать перерасхода ресурсов при стабильной работе и обеспечит необходимые ресурсы для инициализации.

Для масштабирования использую HorizontalPodAutoscaler (HPA) Метрика для масштабирования: загрузка CPU. При средней загрузке CPU 50% поды начинают масштабироваться. Это обеспечивает экономию ресурсов ночью и производительность днём.

Отказоустойчивость Так как кластер мультизональный с 5 нодами мы можем обеспечить высокую отказаустойчивость, чтобы сбой одной ноды не затронул сразу несколько подов приложения. Для того, чтобы поды не поднимались на одной и той-же ноде я использовал антиаффинность подов.

Проверка готовности приложения (readinessProbe) Приложению требуется 5–10 секунд для инициализации. Без проверки готовности может случиться отправка запросов на неподготовленные поды, что приведет к потере запроса.

Проверку буду осуществлять через HTTP-запрос к маршруту /health. initialDelaySeconds: 10, чтобы учесть время инициализации.
