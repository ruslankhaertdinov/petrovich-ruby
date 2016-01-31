![Petrovich](petrovich.png)

Склонение падежей русских имён, фамилий и отчеств. Вы задаёте начальное имя
в именительном падеже, а получаете в нужном вам.

Petrovich также позволяет определять пол по имени, фамилии, отчеству.

[![Build Status](https://travis-ci.org/petrovich/petrovich-ruby.svg?branch=dev-1.0)](https://travis-ci.org/petrovich/petrovich-ruby)

## Установка

Добавьте в Gemfile:

```ruby
gem 'petrovich', '~> 1.0'
```

Установите гем cредствами Bundler:

    $ bundle

Или установите его отдельно:

    $ gem install petrovich

## Зависимости

Для работы гема требуется Ruby не младше версии 1.9.3. Petrovich не
привязан к Ruby on Rails и может свободно использоваться практически
в любых приложениях и библиотеках на языке Ruby.

## Использование

Вы задаёте начальные значения (фамилию, имя и отчество) в именительном
падеже, а библиотека делает всё остальное. Если вам известен пол - укажите его, это повысит скорость работы и даст более точные результаты. Если пол не указан, то Petrovich попытается определить его автоматически. Примеры:

```ruby
# Склонение в дательный падеж при помощи метода `dative`. Существуют методы `genitive`,
# `dative`, `accusative`, `instrumental`, `prepositional`.
Petrovich(
  lastname: 'Салтыков-Щедрин',
  firstname: 'Михаил',
  middlename: 'Евграфович',
).dative.to_s # => Салтыкову-Щедрину Михаилу Евграфовичу

# Склонение в творительный падеж с использованием метода `to` и возвратом отчества.
# Аналогично можно вызвать метод `firstname`, чтобы получить имя.
Petrovich(
  firstname: 'Иван',
  middlename: 'Петрович',
).to(:instrumental).middlename # => Петровича Ивана

# Склонение с указанием пола. В данном случае по имени и фамилии не возможно определить пол
# человека, поэтому, если вам известен пол, всегда передавайте его в аргументах,
# чтобы склонение было верным.
# Если пол неизвестен, то гем попытается определить его самостоятельно.
# Пол должен быть указан в виде строки или символа, возможные значения: male, female.
Petrovich(
  lastname: 'Андрейчук',
  firstname: 'Саша',
  gender: :male
).to(:instrumental).to_s # => Андрейчуку Саше
```

Полный список поддерживаемых методов склонения приведён
в таблице ниже.

| Метод          | Падеж        | Характеризующий вопрос |
|----------------|--------------|------------------------|
| genitive       | родительный  | Кого?                  |
| dative         | дательный    | Кому?                  |
| accusative     | винительный  | Кого?                  |
| instrumental   | творительный | Кем?                   |
| prepositional  | предложный   | О ком?                 |

### Определение пола

Примеры:

```ruby
Petrovich(
  lastname: 'Склифасовский'
).gender # => :male

Petrovich(
  firstname: 'Александра',
  lastname: 'Склифасовская'
).female? # => true

Petrovich(
  lastname: 'Склифасовский'
).male? # => true

Petrovich(
  firstname: 'Саша',
  lastname: 'Андрейчук'
).androgynous? # => true

Petrovich(
  firstname: 'Саша',
  lastname: 'Андрейчук'
  gender: :male,
).male? # => true
```

## CLI

Примеры вызовов:

```bash
petrovich -l Иванов -f Иван -m Иванович -g male -t accusative
petrovich -l Иванов -f Иван -m Иванович -t accusative -d
petrovich -l Иванов -f Иван -m Иванович -t accusative -o
```

Подробное руководство: `petrovich inflect --help`

## Модульные тесты

Для запуска тестов достаточно выполнить команду `rake`.
Чтобы запустить тесты "аккуратности" по словарю фамилий, выполните команду `rake evaluate`, после выполнения вы увидите подробный отчёт.

## Разработчики

 * [Андрей Козлов](https://github.com/tanraya)
 * [Дмитрий Усталов](http://ustalov.name)

## Благодарности

Эта библиотека не была бы столь замечательна без содействия Павла Скрылёва,
Никиты Помящего, Игоря Бочкарёва, и других хороших людей.

## Портирование

Существуют официальные порты Petrovich на другие языке и платформы. Их список
доступен по адресу <https://github.com/petrovich>. Ребята, спасибо!

## Дальнейшее развитие

Мы планируем и далее улучшать этот проект. Поэтому нам важен отклик от
пользователей данного гема.

Наши приоритеты следующие:

* Повысить точность склонения женских фамилий до 98%
* Обрабатывать ФИО в тексте. Например, чтобы выделить ФИО в виде гиперссылки. Не обязательно только ФИО, возможно просто фамилии, имена и так далее.
* Перевести реализацию на язык Rust. Это даст возможность использовать библиотеку в различных языках, подключая её через FFI и не потребует отдельной реализации в каждом языке. Это также увеличит скорость работы библиотеки.

Если вы хотите помочь этому проекту, вы можете реализовать любую
вышеперечисленную идею. Перед этим желательно связаться с нами,
чтобы мы были в курсе. Если у вас появилось такое желание, напишите в issues.

## Содействие

Если вы нашли баги как программной части, так и в базе правил, то вы всегда
можете форкнуть репозиторий и внести необходимые изменения. Ваша помощь не
останется незамеченной! Если вы заметили ошибки при склонении падежей имён,
фамилий или отчеств, можете написать об этом в Issues на GitHub.
Проблема будет сразу же исследована и, по возможности, решена.

Не стесняйтесь добавлять улучшения.

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
