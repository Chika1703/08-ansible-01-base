# Отчёт по домашнему заданию «Основы Ansible»

**Студент:** *Колб Дмитрий*
**Версия Ansible:** *2.16.3*

---

## Содержание

- [Отчёт по домашнему заданию «Основы Ansible»](#отчёт-по-домашнему-заданию-основы-ansible)
  - [Содержание](#содержание)
  - [Введение](#введение)
  - [Описание проекта](#описание-проекта)
  - [Структура репозитория и конфигурационные файлы](#структура-репозитория-и-конфигурационные-файлы)
    - [inventory/prod.yml](#inventoryprodyml)
    - [inventory/test.yml](#inventorytestyml)
    - [site.yml](#siteyml)
    - [start\_containers.yml](#start_containersyml)
    - [group\_vars](#group_vars)
  - [выполнение пунктов:](#выполнение-пунктов)
  - [Заключение](#заключение)

---

## Введение

Домашнее задание посвящено знакомству с базовыми механизмами работы Ansible. Основная цель — отработка навыков управления конфигурациями и работы с переменными, включая зашифрованные значения, с помощью `ansible-vault`.

---

## Описание проекта

Были выполнены следующие шаги:

1. Запуск контейнеров CentOS 7 и Ubuntu через Ansible.
2. Настройка переменных в group\_vars и проверка их корректной подстановки.
3. Использование `ansible-vault` для защиты чувствительной информации.
4. Работа с подключением `local` на управляющем хосте.
5. Визуальная проверка выполнения каждого шага с помощью скриншотов.

---

## Структура репозитория и конфигурационные файлы

### inventory/prod.yml

```yaml
all:
  children:
    el:
      hosts:
        centos7:
          ansible_connection: docker
    deb:
      hosts:
        ubuntu:
          ansible_connection: docker
    local:
      hosts:
        localhost:
          ansible_connection: local
```

*Комментарий:* Основной инвентори-файл с разделением на группы `el`, `deb` и `local`.

---

### inventory/test.yml

```yaml
all:
  children:
    local:
      hosts:
        localhost:
          ansible_connection: local
```

*Комментарий:* Тестовое окружение с `localhost` в отдельной группе `local`.

---

### site.yml

```yaml
- import_playbook: start_containers.yml

- name: Print some_fact from all hosts
  hosts: all
  gather_facts: false
  tasks:
    - name: Print group-specific some_fact
      debug:
        msg: "{{ some_fact }}"
```

*Комментарий:* Главный playbook, который сначала запускает контейнеры, а затем выводит значения переменной `some_fact`.

---

### start\_containers.yml

```yaml
- name: Manage Docker containers
  hosts: localhost
  tasks:
    - name: Ensure centos7 container is running
      community.docker.docker_container:
        name: centos7
        image: centos:7
        state: started
        restart_policy: unless-stopped
        command: "sleep 3600"
        privileged: yes

    - name: Ensure ubuntu container is running
      community.docker.docker_container:
        name: ubuntu
        image: ubuntu
        state: started
        restart_policy: unless-stopped
        command: "sleep 3600"
        privileged: yes
```

*Комментарий:* Управление Docker-контейнерами средствами Ansible.

---

### group\_vars

* `group_vars/all/examp.yml`

```yaml
some_fact: "all default fact"
```

* `group_vars/deb/examp.yml`

```yaml
some_fact: "deb default fact"
```

* `group_vars/el/examp.yml`

```yaml
some_fact: "el default fact"
```

*Комментарий:* Определение значений `some_fact` в зависимости от групп хостов.

---

## выполнение пунктов:

* **Пункт 1: Значение `some_fact` для localhost в test.yml**
  ![Пункт 1](https://github.com/Chika1703/08-ansible-01-base/blob/main/08-ansible-01-base/img/1.jpg)

* **Пункт 2: Изменённое значение `some_fact` в group\_vars/all**
  ![Пункт 2](https://github.com/Chika1703/08-ansible-01-base/blob/main/08-ansible-01-base/img/2.jpg)

* **Пункт 3–4: Запуск контейнеров и получение фактов по prod.yml**
  ![Пункты 3–4](https://github.com/Chika1703/08-ansible-01-base/blob/main/08-ansible-01-base/img/3.jpg)

* **Пункт 5–6: Значения `some_fact` после настройки групп el и deb**
  ![Пункты 5–6](https://github.com/Chika1703/08-ansible-01-base/blob/main/08-ansible-01-base/img/4.jpg)

* **Пункт 7–8: Зашифрованные group\_vars и успешный запуск с vault-паролем**
  ![Пункты 7–8](https://github.com/Chika1703/08-ansible-01-base/blob/main/08-ansible-01-base/img/5.jpg)

* **Пункт 9: Список и описание плагина подключения**
  Команды:

  ```bash
  ansible-doc -t connection -l
  ansible-doc -t connection local
  ```

* **Пункт 10–11: Группа `local` и успешная работа всех facts**
  ![Пункты 10–11](https://github.com/Chika1703/08-ansible-01-base/blob/main/08-ansible-01-base/img/7.jpg)

---

## Заключение

В результате выполнения задания были:

* Настроены и запущены Docker-контейнеры средствами Ansible.
* Созданы и протестированы групповые переменные с условной логикой.
* Использован `ansible-vault` для защиты чувствительных данных.
* Проведена отладка подключения и структуры инвентори-файлов.

Все этапы успешно пройдены, результаты подтверждены выводами команд и скриншотами.
