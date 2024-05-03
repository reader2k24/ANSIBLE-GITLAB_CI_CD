# 1. Клонирование репозитория GitHub
Этот репозиторий содержит Ansible Playbook и GitLab CI/CD для конфигурации серверов.

## Использование
Склонируйте этот репозиторий на ваш управляющий хост:

    git clone https://github.com/reader2k24/ANSIBLE-GITLAB_CI_CD.git

Перейдите в склонированный репозиторий:

    cd ANSIBLE

# 2. Установка и настройка Ansible (локально)

Этот раздел содержит инструкции по установке и настройке Ansible на локальной системе, а также процесс подключения к удаленному серверу через SSH для управления им с помощью Ansible.

## 2.1. Установка Ansible

1. Обновите список пакетов:

    ```bash
    sudo apt update
    ```

2. Установите Ansible:

    ```bash
    sudo apt install ansible
    ```

## 2.2. Настройка удаленного сервера

1. Убедитесь, что на вашем удаленном сервере включена SSH аутентификация для root-пользователя. Для этого отредактируйте файл конфигурации SSH:

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

    Измените следующие параметры:

    ```
    Port 222*
    PermitRootLogin yes
    ```

2. Перезагрузите службу SSH:

    ```bash
    sudo systemctl restart ssh
    ```

## 2.3. Подключение к удаленному серверу и запуск Ansible

1. Получите IP-адрес вашего удаленного сервера:

    ```bash
    ip a
    ```

2. Скопируйте ваш открытый SSH ключ (id_rsa.pub) в файл authorized_keys на удаленном сервере:

    ```bash
    ssh-copy-id -i ~/.ssh/id_rsa.pub -p 222* root@IP_адрес
    ```

    Если файл id_rsa.pub отсутствует, создайте его с помощью команды:

    ```bash
    ssh-keygen
    ```

3. Введите пароль root-пользователя удаленного сервера, если это необходимо:

    ```bash
    sudo passwd root
    ```

4. Измените файл `inventory/hosts.ini` для соответствия вашим серверам. Замените IP-адреса, порты и имена пользователя на ваши данные.

5. Отредактируйте файл `roles/test/main.yml`, если необходимо изменить задачи, выполняемые на серверах.

6. Запустите playbook с помощью команды `ansible-playbook`:

    ```bash
    ansible-playbook -i inventory/hosts.ini all.yml
    ```

# 3. Установка проекта MY_TEST

Этот раздел содержит инструкции по установке проекта MY_TEST с использованием GitLab.

## 3.1. Создание репозитория

1. Зайдите на GitLab и создайте новый проект.

2. Выберите опцию "Create blank project".

3. Укажите название проекта: MY_TEST.

4. Создайте пустой репозиторий без добавления README.md.

5. Скопируйте ссылку для клонирования репозитория в формате HTTPS.

## 3.2. Клонирование репозитория
1. Удалите текущую историю Git:

    ```bash
    rm -rf .git
    ```
2. Инициализируйте новый репозиторий Git:

    ```bash
    git init
    ```
3. Добавьте все файлы в индекс Git:

    ```bash
    git add .
    ```
4. Сделайте первый коммит:

    ```bash
    git commit -m "Новый проект"
    ```
5. Склонируйте репозиторий с GitLab:

    ```bash
    git clone https://gitlab.com/ci_cd*ID_CI_CD*/my_test.git .
    ```
6. Переключитесь на основную ветку (main):

    ```bash
    git switch --create main
    ```
7. Проверьте, что удаленный репозиторий правильно настроен:

    ```bash
    git remote -v
    ```
8. Если удаленный репозиторий не настроен, добавьте его:

    ```bash
    git remote add origin https://gitlab.com/*ID_CI_CD*/my_test.git
    ```
9. Отправьте изменения на удаленный репозиторий:

    ```bash
    git push --set-upstream origin main
    ```
11. Теперь ваш репозиторий склонирован и настроен для дальнейшей работы.

# 4. Установка и настройка CI/CD с помощью GitLab Runner

Этот раздел содержит инструкции по установке и настройке CI/CD с использованием GitLab Runner.

## 4.1 Загрузка и установка GitLab Runner

1. Загрузите двоичный файл GitLab Runner:

    ```bash
    sudo wget -O /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64
    ```

2. Измените разрешения файла GitLab Runner на выполнение:

    ```bash
    sudo chmod +x /usr/local/bin/gitlab-runner
    ```

3. Создайте пользователя GitLab Runner:

    ```bash
    sudo useradd --comment 'GitLab Runner' --create-home gitlab-runner --shell /bin/bash
    ```

4. Установите и запустите GitLab Runner в качестве службы:

    ```bash
    sudo gitlab-runner install --user=gitlab-runner --working-directory=/home/gitlab-runner
    sudo gitlab-runner start
    ```

## 4.2. Настройка GitLab Runner
1. Создайте новый runner:

    ```bash
    sudo gitlab-runner register
    ```
2. Введите URL вашего экземпляра GitLab:

    ```bash
    https://gitlab.com/
    ```
3. Введите токен регистрации, который можно найти в настройках проекта GitLab: Settings -> CI/CD -> Runners -> Project runners -> Registration token (скопируйте).
 
   ```bash
   
   ```
4. Введите описание для runner:

    ```bash
    [debian]: new runners
    ```
5. Укажите теги для runner (через запятую):

    ```bash
    servers
    ```
6. Опционально введите заметку для обслуживания runner.

   ```bash
   
   ```
7. Выберите тип executor. В этом случае:

    ```bash
    ssh
    ```
8. Укажите адрес сервера SSH:

    ```bash
    IP VM
    ```
9. Укажите порт SSH сервера:

    ```bash
    PORT VM
    ```
10. Укажите пользователя SSH:

    ```bash
    root
    ```
11. Введите пароль пользователя SSH:

    ```bash
    PASSWD ROOT (VM)
    ```
12. Укажите путь к файлу идентификации SSH (если используется):

    ```bash
    /root/.ssh/id_rsa
    ```

## 4.3. Настройка SSH (VM)
1. Создайте файл known_hosts:

    ```bash
    touch /root/.ssh/known_hosts
    ```
2. Измените разрешения файла known_hosts:
    ```bash
    chmod 600 /root/.ssh/known_hosts
    ```

3. Добавьте свой собственный IP адрес в файл known_hosts:
    ```bash
    ssh-keyscan -H *IP >> ~/.ssh/known_hosts
    ```
4. Войдите по SSH в собстенную учетную запись (root):

    ```bash
    ssh root@*IP -p 222*
    ```
5. Добавьте свой собственный id_rsa в файл authorized_keys, при отсутсвие файла id_rsa.pub применить команду `ssh-keygen:`
    ```bash
    ssh-copy-id -i /root/.ssh/id_rsa.pub -p 2222 root@*IP
    ```
## 4.4. Проведение CI (GitLab)
Перейдите в свой репозиторий GitLab и запустить Pipelines 
