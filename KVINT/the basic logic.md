
-> Портал собирает билд
-> Билд при деплое попадает в [бота](https://gitlab.prod.kvint.io/kvint-core/kvint-bot)
-> [бот](https://gitlab.prod.kvint.io/kvint-core/kvint-bot) парсит сообщения для ттс, собирает в список, и передает [дайлеру](https://gitlab.prod.kvint.io/kvint-core/kvint-dialer-go)
-> [Дайлер](https://gitlab.prod.kvint.io/kvint-core/kvint-dialer-go) перенаправляет ттс сообщения на ттс [прокси](https://gitlab.prod.kvint.io/yrasiq/tts-proxy)
-> ттс [прокси](https://gitlab.prod.kvint.io/yrasiq/tts-proxy) парсит запрос собирает данные и направляет данные в [воркера](https://gitlab.prod.kvint.io/yrasiq/oratio-runner) модели
-> модель генерирует аудио и отдает [прокси](https://gitlab.prod.kvint.io/yrasiq/tts-proxy)
-> прокси меняет аудио под переданные настройки, кеширует результат и отправляет [дайлеру](https://gitlab.prod.kvint.io/kvint-core/kvint-dialer-go) .