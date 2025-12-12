# Инструкция по запуску передачи по API

## Быстрый старт

### Шаг 1: На ведомом узле (где работает CRSF)

Откройте **два терминала**:

**Терминал 1** - запустите основное приложение:
```bash
sudo ./crsf_io_rpi
```

**Терминал 2** - запустите API интерпретатор:
```bash
./crsf_api_interpreter 8082
```

### Шаг 2: На ведущем узле (откуда будете управлять)

Запустите API сервер:
```bash
./crsf_api_server 8081 <IP_ведомого_узла> 8082
```

**Пример:**
```bash
# Если ведомый узел на localhost
./crsf_api_server 8081 localhost 8082

# Если ведомый узел по IP адресу
./crsf_api_server 8081 192.168.1.100 8082
```

### Шаг 3: Отправка команд

Теперь можно отправлять команды через HTTP API или Python:

#### Через curl:
```bash
# Установить канал 1 в значение 1500
curl -X POST http://localhost:8081/api/command/setChannel \
  -H "Content-Type: application/json" \
  -d '{"channel":1,"value":1500}'

# Установить режим manual
curl -X POST http://localhost:8081/api/command/setMode \
  -H "Content-Type: application/json" \
  -d '{"mode":"manual"}'

# Отправить каналы
curl -X POST http://localhost:8081/api/command/sendChannels \
  -H "Content-Type: application/json" \
  -d '{}'
```

#### Через Python:
```python
from api_wrapper import CRSFAPIWrapper

# Создать обертку
crsf = CRSFAPIWrapper("http://localhost:8081")
crsf.auto_init()

# Установить режим manual
crsf.set_work_mode("manual")

# Установить канал 1
crsf.set_channel(1, 1500)

# Отправить каналы
crsf.send_channels()
```

#### Через графический интерфейс:
```bash
python3 crsf_realtime_interface.py --api --api-url http://localhost:8081
```

## Проверка работы

1. Убедитесь, что все три процесса запущены:
   - `crsf_io_rpi` на ведомом узле
   - `crsf_api_interpreter` на ведомом узле
   - `crsf_api_server` на ведущем узле

2. Проверьте, что порты не заняты:
```bash
# На ведомом узле
netstat -tuln | grep 8082

# На ведущем узле
netstat -tuln | grep 8081
```

3. Проверьте файл команд на ведомом узле:
```bash
cat /tmp/crsf_command.txt
```

## Параметры по умолчанию

- **API Server порт:** 8081
- **API Interpreter порт:** 8082
- **Файл команд:** `/tmp/crsf_command.txt`

## Устранение проблем

### Ошибка "порт занят"
```bash
# Найдите процесс, использующий порт
sudo lsof -i :8081
sudo lsof -i :8082

# Убейте процесс или используйте другой порт
```

### Ошибка подключения к API серверу
- Убедитесь, что API сервер запущен
- Проверьте firewall настройки
- Проверьте правильность IP адреса ведомого узла

### Команды не выполняются
- Убедитесь, что `crsf_io_rpi` запущен
- Проверьте права на запись в `/tmp/crsf_command.txt`
- Проверьте логи `crsf_api_interpreter`

## Дополнительная информация

Подробная документация: [docs/API_SERVER_README.md](docs/API_SERVER_README.md)

