# SML Miner — High Performance Tensor Core Miner (Pearl V3)

**SML Miner** — высокопроизводительный Multi-GPU майнер для алгоритма **Pearl Network (Consensus V3)**, максимально раскрывающий потенциал тензорных ядер видеокарт **NVIDIA Ampere, Ada Lovelace и Blackwell** (RTX 30xx, 40xx, 50xx).

[![GitHub Release](https://img.shields.io/github/v/release/alex100off/sml_miner)](https://github.com/alex100off/sml_miner/releases)
[![Platform](https://img.shields.io/badge/Platform-Linux%20x86__64-blue.svg)]()
[![CUDA](https://img.shields.io/badge/CUDA-Compute%208.0%2B-green.svg)]()

---

## ⚡ Хэшрейт на популярных видеокартах

Майнер использует глубоко оптимизированный тензорный конвейер **CUTLASS 3.x INT8 MMA**, обеспечивая **~93% физического предела кремния**:

| Видеокарта | Хэшрейт (TH/s) | Время расчета нонса | Рекомендуемый Power Limit |
| :--- | :---: | :---: | :---: |
| **RTX 3060 12GB** | **~71 TH/s** | ~248 мс | 115 W |
| **RTX 3070 8GB** | **~88 TH/s** | ~200 мс | 140 W |
| **RTX 3080 10GB** | **~115–125 TH/s** | ~140–155 мс | 220–240 W |
| **RTX 4070 Ti 12GB** | **~165 TH/s** | ~105 мс | 170 W |
| **RTX 4080 16GB** | **~220 TH/s** | ~80 мс | 230 W |
| **RTX 4090 24GB** | **~310–330 TH/s** | ~55 мс | 330–350 W |
| **8× RTX 4090 (Риг)** | **~2.48–2.60 PH/s** | — | Линейное масштабирование |

---

## 🚀 Быстрый старт (Portable бинарник)

Майнер поставляется в виде автономного архива со встроенным CUDA Runtime. **Устанавливать CUDA Toolkit, Python, GCC или компилировать проект НЕ нужно**. Требуется только стандартный драйвер NVIDIA (535+).

### Запуск в Ubuntu / Debian
```bash
# 1. Скачайте свежий архив из раздела Releases:
wget https://github.com/alex100off/sml_miner/releases/download/v1.0.0/sml_miner_v1.0.0_linux_x64.tar.gz

# 2. Распакуйте:
tar -xzvf sml_miner_v1.0.0_linux_x64.tar.gz

# 3. Запустите майнинг (авто-детект подцепит все видеокарты в риге):
./run.sh --backend cuda --tc --pool stratum+tcp://prl-ru.kryptex.network:7048 --wallet ВАШ_КОШЕЛЕК --worker rig01
🐧 Запуск на HiveOS
Быстрый запуск через Hive Shell
В веб-интерфейсе HiveOS остановите стандартный майнер: miner stop.
Откройте Remote Access -> Hive Shell Start.
Выполните:
code
Bash
mkdir -p /hive/miners/sml_miner && cd /hive/miners/sml_miner
wget https://github.com/alex100off/sml_miner/releases/download/v1.0.0/sml_miner_v1.0.0_linux_x64.tar.gz
tar -xzvf sml_miner_v1.0.0_linux_x64.tar.gz

# Запуск в фоновом окне screen:
screen -S sml ./run.sh --backend cuda --tc --pool stratum+tcp://prl-ru.kryptex.network:7048 --wallet ВАШ_КОШЕЛЕК --worker rig01
Нажмите Ctrl + A, затем D для отключения от экрана (майнинг продолжит идти в фоне). Чтобы вернуться к логу: screen -r sml.
📋 Полный список аргументов командной строки
code
Text
Использование: ./run.sh [ПАРАМЕТРЫ]

Основные параметры:
  --pool URI         Адрес Stratum-пула (формат: stratum+tcp://хост:порт)
  --wallet ADDR      Адрес вашего Pearl-кошелька (prl1p...)
  --worker NAME      Имя рига/воркера (по умолчанию: rig01)
  -p, --password STR Пароль авторизации или кастомный diff (по умолчанию: x)
  --agent NAME       Маскировка User-Agent на пуле (по умолчанию: krig/1.5.6)
  --backend NAME     Бэкенд вычислений: cuda (по умолчанию)

Управление видеокартами:
  --devices N[,M]    Индексы используемых GPU через запятую (например: --devices 0,1,3).
                     Если параметр НЕ указан — майнер АВТОМАТИЧЕСКИ находит и задействует
                     все подходящие видеокарты (Ampere / Ada / Blackwell).
  --list-devices     Вывести список обнаруженных видеокарт NVIDIA и выйти.
  --tc               Включить тензорный движок Tensor Cores (обязательно для максимальной скорости).

Тестирование и отладка:
  --mock             Автономный тест (поиск первой валидной шары с верификацией ZK-Proof).
  --max-nonce N      Остановиться после проверки N нонсов.
  --help             Показать встроенную справку.
💡 Примеры команд
1. Майнинг на Kryptex со всеми картами в риге:
code
Bash
./run.sh --backend cuda --tc --pool stratum+tcp://prl-ru.kryptex.network:7048 --wallet prl1p48umhsfg34pklx2yj02egu9csk7s2khrvgerr6w79p8pa4wp8u9qu994sr --worker rig01
2. Майнинг на LuckyPool (с адаптивным Vardiff):
code
Bash
./run.sh --backend cuda --tc --pool stratum+tcp://pearl-cpu-eu1.luckypool.io:3370 --wallet prl1p48umhsfg34pklx2yj02egu9csk7s2khrvgerr6w79p8pa4wp8u9qu994sr --worker rig01 -p x
3. Выбор только определенных карт (например, GPU 0 и 2):
code
Bash
./run.sh --backend cuda --tc --devices 0,2 --pool stratum+tcp://prl-ru.kryptex.network:7048 --wallet ВАШ_КОШЕЛЕК --worker rig01
🔧 Тонкая настройка и разгон (Overclocking)
Алгоритм Pearl V3 на 90% зависит от вычислительной мощности тензорных ядер и на 10% — от видеопамяти:
Фиксация частоты ядра (Lock Core Clock):
Видеокарты NVIDIA сбрасывают частоты при прогреве. Чтобы хэшрейт не падал со временем, зафиксируйте частоту ядра через драйвер:
code
Bash
# Для RTX 3060:
sudo nvidia-smi -lgc 1920

# Для RTX 3080:
sudo nvidia-smi -lgc 1750

# Для RTX 4090:
sudo nvidia-smi -lgc 2550
Память (Memory Clock):
Высокий разгон памяти не требуется. Достаточно оставить +0 или небольшие +200..+500 МГц.
Power Limit:
Рекомендуется зажимать PL на 70–80% от максимума карты. Потеря хэшрейта составит всего 3–5%, но температуры и энергопотребление упадут кардинально.
⚖️ Комиссия разработчика (DevFee)
В майнер встроена фиксированная комиссия разработчика 1.5%.
Майнер работает 59 минут непрерывно на ваш кошелек, после чего бесшовно переключается ровно на 54 секунды на сессию комиссии и возвращается обратно. Никаких микро-разрывов связи и потери хэшрейта.
