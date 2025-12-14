#!/bin/bash

echo "--- Запуск пакетной установки приложений через APT ---"

# Обновляем список пакетов
sudo apt update
if [ $? -ne 0 ]; then
    echo "❌ Ошибка обновления APT. Проверьте подключение или права sudo."
    exit 1
fi

required_packages=(
    git               # Система контроля версий
    python3           # Python
    vlc               # Медиаплеер
    curl              # Инструмент для передачи данных
    build-essential   # Основные инструменты разработки (компиляторы)
    snapd             # Если нужно установить приложения через Snap
)

# Установка основных пакетов через apt
echo "--- Установка основных APT-пакетов ---"
sudo apt install -y "${required_packages[@]}"

# Установка приложений через Snap (если apt не имеет последних версий)
echo "--- Установка приложений через SNAP ---"
snap_apps=(
    code              # VS Code
    spotify           # Spotify
    slack             # Slack
)

for app in "${snap_apps[@]}"; do
    echo "Установка SNAP-приложения: $app..."
    sudo snap install "$app" --classic
    if [ $? -eq 0 ]; then
        echo "✅ Успешно установлено: $app"
    else
        echo "❌ Ошибка установки SNAP $app."
    fi
done

echo "--- Пакетная установка завершена ---"
