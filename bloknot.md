# 1. Проверяем, запущен ли Docker
systemctl status docker --no-pager | grep -E "Active:|Loaded:"

# 2. Может ли текущий пользователь работать с Docker?
docker info 2>&1 | head -5

# 3. Пробуем запустить контейнер вручную (без Terraform)
docker run --rm --name test-nginx -d -p 8080:80 nginx:1.21.1
sleep 3
curl -s http://localhost:8080 | grep -i nginx && echo "✅ Nginx работает!" || echo "❌ Проблема с Docker"
docker rm -f test-nginx 2>/dev/nulld

образы скачались руками запускаются контейнера 

ищем проблему почему не запускает terraform


# 1. Проверяем права на сокет Docker
ls -la /var/run/docker.sock

dima@ws-HP:/data/workspace/terraform-docker-lab/environments/local$ ls -la /var/run/docker.sock
srw-rw---- 1 root docker 0 Feb 19 12:38 /var/run/docker.sock
dima@ws-HP:/data/workspace/terraform-docker-lab/environments/local$ 

# 2. Пробуем применить конфигурацию и смотрим ошибку
cd /data/workspace/terraform-docker-lab/environments/local
terraform apply -auto-approve 2>&1 | grep -A5 "Error"

руками запустились 

затем 
Полная очистка — начнём с чистого листа
# Удалить все созданные ресурсы
terraform destroy -auto-approve


# 1. Удаляем ВСЕ контейнеры (включая остановленные)
docker rm -f $(docker ps -aq) 2>/dev/null || true

# 2. Удаляем ВСЕ образы (опционально, но для чистоты)
docker rmi -f $(docker images -q) 2>/dev/null || true

# 3. Очищаем кэш Terraform (состояние и провайдеры)
rm -rf .terraform terraform.tfstate* terraform.tfstate.backup 2>/dev/null || true

# 4. Проверяем чистоту
docker ps -a
ls -la | grep terraform

обновил версию докер, все заработало
выкладываем 

# 1. Перейди в корень проекта
cd /data/workspace/terraform-docker-lab

# 2. Инициализируй локальный репозиторий
git init

# 3. Добавь все файлы
git add .

# 4. Сделай первый коммит
git commit -m "feat: initial commit - Terraform Docker Lab"

# 5. Создай репозиторий на GitHub через CLI
gh repo create terraform-docker-lab \
  --public \
  --description "Terraform Docker Lab - Nginx + WordPress" \
  --source=. \
  --remote=upstream \
  --push

# 6. Проверь, что всё загрузилось
gh repo view --web



