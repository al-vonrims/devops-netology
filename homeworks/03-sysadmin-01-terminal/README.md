# devops-netology

## Домашнее задание к занятию "3.1. Работа в терминале, лекция 1"

#### 1. Установите средство виртуализации [Oracle VirtualBox](https://www.virtualbox.org/).
Выполнено.

#### 2. Установите средство автоматизации [Hashicorp Vagrant](https://www.vagrantup.com/).
Выполнено.

#### 3. В вашем основном окружении подготовьте удобный для дальнейшей работы терминал.
Выполнено.

#### 4. С помощью базового файла конфигурации запустите Ubuntu 20.04 в VirtualBox посредством Vagrant.
Выполнено. Была создана виртуальная машина с именем `VagrantVM_default_1641831956290_90035`

#### 5. Ознакомьтесь с графическим интерфейсом VirtualBox, посмотрите как выглядит виртуальная машина, которую создал для вас Vagrant, какие аппаратные ресурсы ей выделены. Какие ресурсы выделены по-умолчанию?
* CPU - 2
* Memmory - 1024 Mb
* HDD - 64 Gb

#### 6. Ознакомьтесь с возможностями конфигурации VirtualBox через Vagrantfile: [документация](https://www.vagrantup.com/docs/providers/virtualbox/configuration.html). Как добавить оперативной памяти или ресурсов процессора виртуальной машине?
Необходимо добавить в конфигурационный файл `Vagrantfile` следующее: 

    config.vm.provider "virtualbox" do |v|
      v.memory = 2048
      v.cpus = 4
    end

#### 7. Команда `vagrant ssh` из директории, в которой содержится Vagrantfile, позволит вам оказаться внутри виртуальной машины без каких-либо дополнительных настроек. Попрактикуйтесь в выполнении обсуждаемых команд в терминале Ubuntu.
Выполнено.

#### 8. Ознакомиться с разделами `man bash`, почитать о настройках самого bash:  
* какой переменной можно задать длину журнала `history`, и на какой строчке manual это описывается?
    + Длина журнала задаётся переменной `HISTSIZE`. Описывается на строке 595.
* что делает директива `ignoreboth` в bash?
    + Значение `ignoreboth` является сокращением для `ignorespace` и `ignoredups`.

#### 9. В каких сценариях использования применимы скобки `{}` и на какой строчке `man bash` это описано?
Используются в составных командах. Описано на строке 197.

#### 10. Основываясь на предыдущем вопросе, как создать однократным вызовом `touch` 100000 файлов? А получилось ли создать 300000? Если нет, то почему?
Создать 100000 файлов можно вызовом команды `touch {1..100000}`.  
При попытке создать 300000 файлов вызовом `touch {1..300000}` получаем ошибку ` Argument list too long`  
Финальный размер агрументов команды превысил размер буфера агрументов:

```bash
    getconf ARG_MAX
    2097152
````

#### 11. В man bash поищите по `/\[\[`. Что делает конструкция `[[ -d /tmp ]]`
Конструкция проверяет наличие директории `/tmp`. 

#### 12. Основываясь на знаниях о просмотре текущих (например, PATH) и установке новых переменных; командах, которые мы рассматривали, добейтесь в выводе type -a bash в виртуальной машине наличия первым пунктом в списке:

```bash
bash is /tmp/new_path_directory/bash
bash is /usr/local/bin/bash
bash is /bin/bash
```
Для получения результата были использованы следующие команды:

```bash
mkdir /tmp/new_path_directory
cp /bin/bash /tmp/new_path_directory/bash
PATH=/tmp/new_path_directory:$PATH
```

#### 13. Чем отличается планирование команд с помощью `batch` и `at`?
`batch` - Выполняет команды в зависимости от нагруженности системы.  
`at` - Выполняет команды в определённое время.

#### 14. Завершите работу виртуальной машины чтобы не расходовать ресурсы компьютера и/или батарею ноутбука.
Выполнено.