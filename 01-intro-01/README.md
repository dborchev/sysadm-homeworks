# Домашнее задание к занятию «1.1. Введение в DevOps»

## Задание №1 - Подготовка рабочей среды

1. Установить Py Charm Community Edition: https://www.jetbrains.com/ru-ru/pycharm/download/ - это бесплатная версия IDE. 
Если у вас уже установлен любой другой продукт от JetBrains,то можно использовать его. ✅
1. Установить плагины:
    - Terraform, ✅
    - MarkDown, ✅
    - Yaml/Ansible Support, ✅
    - Jsonnet. ✅
1. Склонировать текущий репозиторий или просто создать файлы для проверки плагинов:
    - [netology.tf](netology.tf) – терраформ,
    - [netology.sh](netology.sh) – bash,
    - [netology.md](netology.md) – markdown, 
    - [netology.yaml](netology.yaml) – yaml,
    - [netology.jsonnet](netology.jsonnet) – jsonnet.
1. Убедитесь, что работает подсветка синтаксиса ✅
1. Добавьте свое имя в каждый файл, сделайте снимок экран и загрузите его на любой сервис обмена картинками.
    - Terraform: ![Терраформ](task_1_screenshots/tf.JPG)
    - Bash: ![bahs](task_1_screenshots/sh.JPG)
    - Markdown: ![markdown](task_1_screenshots/md.JPG)
    - Yaml: ![Yaml](task_1_screenshots/yaml.JPG)
    - Jsonnet: ![Jsonnet](task_1_screenshots/jsonnet.JPG)

## Задание №2 - Описание жизненного цикла задачи (разработки нового функционала)

Чтобы лучше понимать предназначение дальнейших инструментов, с которыми нам предстоит работать, давайте 
составим схему жизненного цикла задачи в идеальном для вас случае.

### Описание истории

Представьте, что вы работаете в стартапе, который запустил интернет-магазин. Ваш интернет-магазин достаточно успешно развивался, и вот пришло время налаживать процессы: у вас стало больше конечных клиентов, менеджеров и разработчиков. Сейчас от клиентов вам приходят задачи, связанные с разработкой нового функционала. Задач много, и все они требуют выкладки на тестовые среды, одобрения тестировщика, проверки менеджером перед показом клиенту. В случае необходимости, вам будет необходим откат изменений. 

### Постановка задачи

Вам необходимо описать процесс решения задачи в соответствии с жизненным циклом разработки программного обеспечения. Использование какого-либо конкретного метода разработки не обязательно. Для решения главное - прописать по пунктам шаги решения задачи (релизации в конечный результат) с участием менеджера, разработчика (или команды разработчиков), тестировщика (или команды тестировщиков) и себя как DevOps-инженера. 

### Решение задачи

####К вопросу о DevOps 

Из каждой группы разработчиков (Dev) одну неделю каждого спринта один человек выделяется как дежурный (онкол) для решения повседневных оперативных (Ops) задач.

К таким задачам относятся, в рамках описанного здесь процесса, ревью кода (на срочных задачах), отладка github actions для автоматизации тестов и процесса развертывания, принятие решение об откате (и методе откате) изменений.

В более общем смысле, онкол решает текущие задачи обслуживания системы, включая мониторинг, метрики, алармы, тикеты по багам (которые могут стать задачами, взависмости от ситуации).

Онкол, поскольку обладает всей информацией о работе сервиса в настоящий момент, имеет решающее слово в одобрении релиза в стадии бета и перевода в прод.

Для распространения опыта и знаний о структуре системы, при росте команды, вновь прибывшие присоединяются к онколу на смену.

Таким образом, все разработчики периодически сталкиваются с проблемами прода (и его улучшения), что собственно и есть DevOps.
+ явный пробел в постановке задачи во фразе "себя как DevOps", девопс -- это не должность, это бизнес-процесс и культура

####О процессе разработки по стадиям:

- все задачи проходят через трекер по стадиям (новая, бэклог, на исполнение, в работе, на ревью, в релиз, завершена)
  - поскольку у нас DevOps, то задачи могут включать не только фичи для пользователей, но и такие оперативные вещи как добавить новых метрик, сделать дашборд для мониторинга, описать изменения инфраструктуры (как кода) 
- задачи в стадию "новая" попадают от "заказчиков" (менеджеров, других разработчиков) и обрабатываются менеджерами и лидами команд разработки (например, оценка сложности, срочности, разбивка на подзадачи при необходимости), после чего попадают в бэклог
- задачи в стадии "на исполнение" набираются в начале двухнедельных спринтов из "бэклога" по мере срочности
- задачу в стадию "в работе" переводит непосредственно исполнитель при начале работы. Технические аспекты:
  1. для каждого сервиса (предположим, что интернет-магазин -- это несколько сервисов) устраиваем отдельное репо. 
  2. репо ведем по монорепному (транковому) принципу
  3. добавление в репо - только через пул-реквесты / ревью кода. Используем функцию github "Require pull request reviews before merging"
  4. К событию PR привязваем github action с тестами, так что тесты становятся обязательной частью PR
     - тесты на этой стадии - преимущественно линтинг, стандартизация, юнит-тесты написанные собственно разработчиками, и наконец тест того, что пакет вообще собирается
     - на этом же этапе полезно внедрить тесты зависимостей, например на уязвимости как в [Dependabot](https://docs.github.com/en/code-security/supply-chain-security/managing-vulnerabilities-in-your-projects-dependencies/about-alerts-for-vulnerable-dependencies)
- задачи в стадию "на ревью" переводит исполнитель после создания PR
- у всех разработчиков несколько поинтов в неделю отведено на ревью кода других разработчиков
  - таким образом, процесс не зависает на ревью слишком долго
- после успешного завершения и мержа PR, исполнитель переводит задачу в стадию "в релиз"
- по мержу PR запускается github action для упаковки (напр. контейнера) и развертывания на стейжинг (стенд)
- на стенде "заказчик" оценивает результат, а онкол проводит тесты (автоматичекие и ручные, где необходимо) готовности
  - тесты для этой стадии разрабатываются преимущественно тестировщиками (при наличии)
- если всё хорошо, изменения развертываются в продакшн
- если все плохо, то онкол откатывает PR 
  - это пораждает еще один PR, который также проходит по вышеописанным стадиям, таким образом все действия прослеживаются по цепочкам PR

