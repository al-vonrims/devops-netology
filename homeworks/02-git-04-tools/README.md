# devops-netology

## Домашнее задание к занятию «2.4. Инструменты Git»

#### 1. Найдите полный хеш и комментарий коммита, хеш которого начинается на `aefea`. 
Полный хеш коммита `aefead2207ef7e2aa5dc81a34aedf0cad4c32545`.  
Комментарий коммита `Update CHANGELOG.md`.  
Значения были найдены с помощью команды `git show aefea --pretty=format:"%H %s" -q`.

#### 2. Какому тегу соответствует коммит `85024d3`? 
Тег данного коммита `v0.12.23`.  
Найден командой `git show 85024d3 --pretty=format:"%D" -q`.

#### 3. Сколько родителей у коммита `b8d720`? Напишите их хеши.
У данного коммита два родителя.  
Их хеши `56cd7859e` `9ea88f22f`.  
Были найдены командой `git show b8d720 --pretty=format:"%p"`.

#### 4. Перечислите хеши и комментарии всех коммитов которые были сделаны между тегами  v0.12.23 и v0.12.24.
Хеши и комментарии коммитов:

    b14b74c49 [Website] vmc provider links
    3f235065b Update CHANGELOG.md
    6ae64e247 registry: Fix panic when server is unreachable
    5c619ca1b website: Remove links to the getting started guide's old location
    06275647e Update CHANGELOG.md
    d5f9411f5 command: Fix bug when using terraform login on Windows
    4b6d06cc5 Update CHANGELOG.md
    dd01a3507 Update CHANGELOG.md
    225466bc3 Cleanup after v0.12.23 release
Результат был получен командой `git log v0.12.23..v0.12.24^ --pretty=format:"%h %s"`.

#### 5. Найдите коммит в котором была создана функция `func providerSource`, ее определение в коде выглядит так `func providerSource(...)` (вместо троеточего перечислены аргументы). 
Данная функция была создана в коммите `8c928e835`.  
Коммит найден с помощью команды:  
`git log -S'func providerSource(' --pretty=format:"%h %ad %an"`.

#### 6. Найдите все коммиты в которых была изменена функция `globalPluginDirs`.
Функция была изменена в следующих коммитах:

    78b122055 Mon Jan 13 16:50:05 2020 -0500 Pam Selle
    52dbf9483 Wed Aug 9 17:46:49 2017 -0400 James Bardin
    41ab0aef7 Wed Aug 9 10:34:11 2017 -0400 James Bardin
    66ebff90c Wed May 3 22:24:51 2017 -0400 James Bardin
    8364383c3 Thu Apr 13 18:05:58 2017 -0700 Martin Atkins

Для поиска были использованы две команды:  
`git grep -p globalPluginDirs`  
`git log -L :globalPluginDirs:plugins.go --pretty=format:"%h %ad %an" -q`

#### 7. Кто автор функции `synchronizedWriters`?
Автор функции `Martin Atkins`.  
Найден с помощью команды `git log -S'func synchronizedWriters(' --pretty=format:"%h %ad %an"`.

