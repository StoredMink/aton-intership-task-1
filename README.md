# Развертывание веб-сервера Nginx с помощью Ansible

В этом проекте представлен плейбук Ansible для автоматизации развертывания и настройки веб-сервера Nginx с поддержкой SSL/TLS.

## Структура проекта

```
nginx-ansible-deployment/
├── ansible.cfg               # Ansible configuration
├── inventory.ini             # Server inventory file
├── nginx_deployment.yml      # Main Ansible playbook
├── files/                    # Static files to be copied to the server
│   ├── html/                 # Web content
│   │   ├── index.html        # Main web page
│   │   └── images/           # Image files
│   └── ssl/                  # SSL certificates
│       ├── mydomain.crt      # SSL certificate
│       └── mydomain.key      # SSL private key
└── templates/                # Jinja2 templates
    └── nginx.conf.j2         # Nginx configuration template
```

## Предварительные условия

- Ansible, установленный на вашей управляющей машине
- Целевой сервер с доступом по SSH
- SSL-сертификаты (или возможность генерировать самоподписанные сертификаты)

## Инструкции по настройке

### 1. Клонируйте/создайте проект

``bash
mkdir -p nginx-ansible-deployment
cd nginx-ansible-deployment
```

### 2. Настройте окружение

1. **Подготовьте файл инвентаризации**:
   
   Отредактируйте файл `inventory.ini`, чтобы включить в него данные о вашем сервере:
   ``ini
 [webservers]
 webserver ansible_host=YOURIP ansible_user=root ansible_password="YOURPASSWD"
 ```

2. **Разместите SSL-сертификаты**:
   
   Скопируйте ваши SSL-сертификаты в каталог `files/ssl/`:

   ```bash
   mkdir -p files/ssl
   cp /path/to/your/certificate.crt files/ssl/mydomain.crt
   cp /path/to/your/private.key files/ssl/mydomain.key
   ```

3. **Подготовка веб-контента**:
   
   Скопируйте файлы вашего сайта:
   ``bash
 mkdir -p files/html/images
 cp /path/to/your/website/index.html files/html/
 ```

### 3. Просмотр и настройка

1. **Просмотрите переменные** в файле `nginx_deployment.yml`:
   
   Настройте любые переменные в соответствии с вашим окружением:
 ``yaml
 vars:
 nginx_port_http: 80
 nginx_port_https: 443
 nginx_server_name: localhost # Измените на ваше доменное имя
 nginx_html_root: /usr/share/nginx/html
 nginx_ssl_dir: /etc/nginx/ssl
 nginx_ssl_cert: mydomain.crt
 nginx_ssl_key: mydomain.key
```

2. **При необходимости настройте конфигурацию Nginx** в файле `templates/nginx.conf.j2`.

### 4. Запустите Playbook

``bash
# Сначала запустите в режиме проверки (изменения вноситься не будут)
ansible-playbook -i inventory.ini nginx_deployment.yml --check

# Затем запустите фактическое развертывание
ansible-playbook -i inventory.ini nginx_deployment.yml
```


## Что делает этот плейбук

1. Устанавливает Nginx
2. Создает необходимые каталоги
3. Копирует SSL-сертификаты
4. Копирует содержимое сайта
5. Настройка Nginx с надлежащими параметрами SSL
6. Запускает и включает службу Nginx
7. Проверяет работу Nginx на указанных портах.

## Соображения безопасности

- Копирование закрытых ключей SSL осуществляется с ограниченными правами (0600)
- HTTP-трафик перенаправляется на HTTPS
- Настроены современные протоколы и шифры TLS
