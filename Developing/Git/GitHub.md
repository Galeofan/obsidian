---
links: https://github.com/Galeofan
download_link: https://desktop.github.com/download/
tags:
  - Github
---
>[!info] Определение
>GitHub - система хранения кода в облаке гитхаб

---
### Создание репозитория

1. Открываем сайт [github.com/new](https://github.com/new) 
2. Введим название репозитория.
3. Жмём Save
4. На вкладке Code проверяем, что выбрана закладка на протокол SSH, копируем SSH ссылку

---
### Привязка репозитория к исходному коду на тачке

> [!warning]
> Все действия делаем в терминале IDEA

1. Выполняем команду для генерации проекта Git: `git init`
2. Прописываем SSH или HTTPS адрес нового репозитория: `git remote add origin СКОПИРОВАННЫЙ_SSH_АДРЕС_РЕПОЗИТОРИЯ` Пример: `git@github.com:Galeofan/-job4j_elementary.git`
3. Выполняем команду для проверки адреса: `git remote -v`
4. Выполняем команду для переключения ветки: `git checkout -b master`
5. Выполняем команду для синхронизации: `git fetch`

---
