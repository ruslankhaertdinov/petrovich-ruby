![Petrovich](petrovich.png)

Склонение падежей русских имён, фамилий и отчеств. Вы задаёте начальное имя
в именительном падеже, а получаете в нужном вам.

[![Build Status](https://secure.travis-ci.org/petrovich/petrovich-ruby.png)](http://travis-ci.org/petrovich/petrovich-ruby)

#### Посмотреть в действии: http://petrovich.rocketscience.it

## Установка

Добавьте в Gemfile:

```ruby
gem 'petrovich'
```

Установите гем cредствами Bundler:

    $ bundle

Или установите его отдельно:

    $ gem install petrovich

## Зависимости

Для работы гема требуется Ruby не младше версии 1.9.1. Petrovich не
привязан к Ruby on Rails и может свободно использоваться практически
в любых приложениях и библиотеках на языке Ruby.

## Использование

Вы задаёте начальные значения (фамилию, имя и отчество) в именительном
падеже, а библиотека делает всё остальное.

```ruby
p = Petrovich.new(:male)
p.lastname('Иванов', :dative)      # => Иванову
p.firstname('Пётр', :dative)       # => Петру
p.middlename('Сергеевич', :dative) # => Сергеевичу
```

Конструктор класса `Petrovich` принимает пол в качестве единственного
аргумента. Пол может принимать значения `:male`, `:female` или
`:androgynous`. Последнее означает, что слово не склоняется по родам.
По нормам языка, некоторые фамилии не склоняются. Например, такие
украинские фамилии, как Симоненко, а также заимствованные фамилии,
такие как Нельсон.

Важно понимать, что явное указание пола повышает точность обработки слов.
Если пол неизвестен, однако известно отчество, то гем постарается
определить по пол отчеству на основе простой эвристики.

### Продвинутое использование

Вы можете подмешать модуль `Petrovich::Extension` в любой класс. Это
особенно полезно при использовании ActiveRecord и подобных ORM.

```ruby
class User < ActiveRecord::Base
  include Petrovich::Extension

  petrovich :firstname  => :my_firstname,
            :middlename => :my_middlename,
            :lastname   => :my_lastname,
            :gender     => :my_gender

  def my_firstname
    'Пётр'
  end

  def my_middlename
    'Петрович'
  end

  def my_lastname
    'Петренко'
  end

  # Если пол не был указан, используется автоматическое определение
  # пола на основе отчества. Если отчество также не было указано,
  # пытаемся определить правильное склонение на основе файла правил.
  def my_gender
    :male # :male, :female или :androgynous
  end
end
```

Ничего не мешает подмешать модуль `Petrovich::Extension` в самый обычный
класс.

```ruby
class Person
  include Petrovich::Extension

  # А здесь мы не указали пол - он будет определяться на основе отчества
  petrovich :firstname  => :name,
            :middlename => :patronymic,
            :lastname   => :surname

  def name
    'Иван'
  end

  def patronymic
    'Олегович'
  end

  def surname
    'Сафронов'
  end
end
```

При помощи метода `petrovich` указываются методы, представляющие фамилию,
имя и отчество. В данном примере указано, что метод `name` представляет
имя, метод `lastname` представляет фамилию, метод `patronymic` представляет
отчество.

После конфигурации Petrovich можно легко выполнять склонение необходимых
строк.

```ruby
user = User.new
user.my_firstname         # => Пётр

user.my_firstname_dative  # => Петру
user.my_middlename_dative # => Петровичу
user.my_lastname_dative   # => Петренко
```

```ruby
person = Person.new
person.name # => Иван

person.my_firstname_dative  # => Ивану
person.my_middlename_dative # => Олеговичу
person.my_lastname_dative   # => Сафронову
```

Базовое имя должно быть в именительном падеже. Вы просто добавляете
`_<суффикс>` в конец имени оригинального метода и получаете заданное имя
в требуемом падеже.

Названия суффиксов для методов образованы от английских названий
соответствующих падежей. Полный список поддерживаемых падежей приведён
в таблице ниже.

| Суффикс метода | Падеж        | Характеризующий вопрос |
|----------------|--------------|------------------------|
| genitive       | родительный  | Кого? Чего?            |
| dative         | дательный    | Кому? Чему?            |
| accusative     | винительный  | Кого? Что?             |
| instrumental   | творительный | Кем? Чем?              |
| prepositional  | предложный   | О ком? О чём?          |

## Лемматизация

Petrovich умеет выполнять преобразовывание фамилии, имени и отчества
в произвольном падеже к нормальной форме, а именно в именительный падеж.
Такой процесс называется *лемматизацией*.

Для выполнения лемматизации нужно передать в методы склонения третий
аргумент, означающий текущий падеж слова, а во втором аргументе указать
желаемый падеж — именительный.

```ruby
p = Petrovich.new(:male )
p.firstname('Савве', :nominative, :dative)         # => Савва
p.middlename('Палладиевичу', :nominative, :dative) # => Палладиевич
p.lastname('Солунскому', :nominative, :dative)     # => Солунский
```

Также Petrovich позволяет преобразовывать слово сразу в любой падеж,
отличный от именительного:

```ruby
p = Petrovich.new(:female )
p.firstname('Марфе', :accusative, :dative)     # => Марфу
p.middlename('Золовне', :accusative, :dative)  # => Золовну
p.lastname('Соловецкой', :accusative, :dative) # => Соловецкую
```

## Оценка точности

Тестирование гема при склонении коллекции фамилий из морфологического
словаря [АОТ] показало точность около 99%. Оригинальный словарь
распространяется по лицензии LGPL версии 2.1, однако используется
только в задаче оценки точности данного гема.

Для оценки точности достаточно выполнить команду `rake evaluate`. После
выполнения этой команды в поток стандартного вывода будут напечатаны
результаты оценки с данными в словаре АОТ.

```
Pr(nominative|male) = 100.0000%
Pr(genitive|male) = 99.7261%
Pr(dative|male) = 99.7510%
Pr(accusative|male) = 99.7635%
Pr(instrumental|male) = 99.2664%
Pr(prepositional|male) = 99.7386%
```
```
Pr(nominative|female) = 100.0000%
Pr(genitive|female) = 99.9102%
Pr(dative|female) = 99.9401%
Pr(accusative|female) = 99.9701%
Pr(instrumental|female) = 99.4636%
Pr(prepositional|female) = 99.9401%
```

В настоящий момент наблюдается точность в 99.7815% на основе обработки
88314 примеров.

[АОТ]: http://seman.svn.sourceforge.net/viewvc/seman/trunk/Docs/Morph_UNIX.txt?revision=HEAD&view=markup

## Модульные тесты

Для запуска тестов достаточно выполнить `rake spec` или просто `rake`.

## Разработчики

 * [Андрей Козлов](https://github.com/tanraya)
 * [Дмитрий Усталов](http://eveel.ru)

## Портирование

Существуют официальные порты Petrovich на другие языке и платформы. Их список
доступен по адресу <https://github.com/petrovich>. Ребята, спасибо!

## Дальнейшее развитие

Мы планируем и далее улучшать этот проект. Поэтому нам важен отклик от
пользователей данного гема. Вот наши планы:

 * добавить отладочный режим, чтобы видеть, какое именно правило было
использовано при словообразовании;
 * интерфейс командной строки, чтобы работать с гемом из командной строки;
 * проверка совместимости с различными ORM и сторонними библиотеками.

Если вы хотите помочь этому проекту, вы можете реализовать любую
вышеперечисленную идею. Перед этим желательно связаться с нами,
чтобы мы были в курсе.

## Содействие

Если вы нашли баги, как программной части, так и в базе правил, то вы всегда
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
