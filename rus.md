# Руководство по использованию Grunt для начинающих: пересмотренное издание

![Логотип Grunt][Логотип Grunt]

Ещё в марте 2013 я написал «[Руководство по использованию Grunt для начинающих][2]», и эта статья стала
самой популярной на сайте. Я написал её когда только начинал знакомство с
[Grunt][3], так что это было в большей степени руководство для
меня самого, чем для кого-то другого. Теперь, спустя полтора года, я чувствую
необходимость пересмотреть свои подходы к использованию Grunt, потому что за
это время я многому научился.

**Если вам не терпится получить сам код, вы можете взять его 
[здесь на Github][4].**

## Глобальная установка Node и Grunt

Во-первых, вам нужно убедиться, что у вас установлен
[Node][5] и [Grunt CLI][6] (command line interface).

* На сайте Node есть загрузочные пакеты для разных систем.
[Полную информацию можно найти здесь][5].
* После установки Node просто запустите следующую команду в вашем терминале
(я использую [iTerm2][7]) для установки **grunt-cli**:

 

    npm install -g grunt-cli


## Установка Ruby и Sass

**Обновление:** я недавно переключился с `grunt-contrib-sass` на `grunt-sass`,
использующий экспериментальный компилятор [libsass][8] на C++,
он быстрее и не требует установки Ruby и gem Sass.
Однако, если у вас возникают проблемы при компиляции с
помощью `grunt-sass`, то, возможно, вам нужно использовать `grunt-contrib-sass`
— в этом случае вам понадобится установка Ruby и gem Sass.

Я использую Sass в качестве CSS-препроцессора. Для использования Sass в
виде Grunt-задачи вам нужно установить Ruby ([полная инструкция по установке
здесь][9]), а после этого gem
[Sass][10]:

    gem install sass


## Создание структуры проекта

Для начала в наш проект нужно добавить несколько директорий, их структура
отражена ниже:

    grunt/
    src/
    src/images/
    src/scripts/
    src/styles/


## Создание Gruntfile

Я больше не использую инструменты-генераторы (такие как `grunt init`
или [Yeoman][11]). Я устанавливаю всё самостоятельно, а это
означает, что я гораздо лучше понимаю что происходит. На самом деле всё не так
сложно, если вы проделаете это несколько раз.

В корне вашего проекта создайте файл `Gruntfile.js`.

Добавьте в этот файл следующий код:

    module.exports = function(grunt) {
     
      require('time-grunt')(grunt);
     
      require('load-grunt-config')(grunt, {
        jitGrunt: true
      });
    };


Верите или нет — это всё, что с нужно сделать с Gruntfile!

`time-grunt` сообщает вам как много времени занимает каждая задача и общее
время сборки, а `jitGrunt: true` указывает `load-grunt-config` использовать
быстрый [jit-grunt][12] (Just In Time)
загрузчик задач (это опционально, но нам же важна скорость?).

## Создание файла пакета

Давайте пойдем дальше и создадим наш базовый файл `package.json`,
через некоторое время он будет содержать в себе зависимости нашего проекта.
Создайте файл и скопируйте в него этот код (очевидно, что нужно сменить ссылку
с «my project» на
настоящее имя вашего проекта):

    {
     "name": "my-project",
     "version": "0.0.1",
     "description": "My project"
    }


## Добавление зависимостей

Теперь у нас есть всё необходимое для того, чтобы добавить несколько модулей.
Запустите по очереди каждую строчку из кода, приведенного ниже:

    npm install grunt --save-dev
    npm install time-grunt --save
    npm install load-grunt-config --save-dev
    npm install grunt-concurrent --save-dev
    npm install grunt-contrib-clean --save-dev
    npm install grunt-contrib-imagemin --save-dev
    npm install grunt-sass --save-dev
    npm install grunt-contrib-uglify --save-dev
    npm install grunt-contrib-jshint --save-dev
    npm install jshint-stylish --save
    npm install grunt-contrib-watch --save-dev


Если вы теперь заглянете в `package.json`, то увидите такое:

    {
     "name": "my-project",
     "version": "0.0.1",
     "description": "My Project",
     "devDependencies": {
      "grunt": "^0.4.5",
      "grunt-concurrent": "^1.0.0",
      "grunt-contrib-clean": "^0.6.0",
      "grunt-contrib-imagemin": "^0.8.1",
      "grunt-contrib-jshint": "^0.10.0",
      "grunt-contrib-uglify": "^0.6.0",
      "grunt-contrib-watch": "^0.6.1",
      "grunt-sass": "^0.16.1",
      "load-grunt-config": "^0.13.1"
     },
     "dependencies": {
      "jshint-stylish": "^1.0.0",
      "time-grunt": "^1.0.0"
     }
    }


Вот более подробное описание того, что мы только что установили:

1. `grunt`: сам исполнитель задач.
2. `time-grunt`: это не обязательное, но весьма полезное дополнение, оно
сообщает, сколько времени занимает каждая задача и общее время выполнения.
3. `load-grunt-config`: позволяет вам содержать ваш основной Gruntfile коротким
и аккуратным, подробнее об этом чуть позже.
4. `grunt-concurrent`: запускает задачи одновременно. Grunt «из-коробки» будет
запускать каждую задачу по очереди, что может затянуться на некоторое время, в
зависимости от количества и типа выполняемых задач, однако часто есть задания,
которые не зависят друг от друга и могут быть запущены одновременно.
5. `grunt-contrib-clean`: очень просто, эта задача удаляет «разные штуки» —
используйте с осторожностью!
6. `grunt-contrib-imagemin`: незаменимая вещь для оптимизации изображений.
7. `grunt-sass`: компиляция ваших SASS/SCSS файлов в CSS. **Обратите
внимание:** эта Sass-задача использует более быстрый компилятор
[libsass][8] компилятор, но он экспериментальный.
Если у вас возникнут проблемы, то лучше используйте более стабильный, но
медленный [grunt-contrib-sass][13].
8. `grunt-contrib-uglify`: делает ваш Javascript красивым и ужасным.
9. `grunt-contrib-jshint`: валидация файлов Javascript.
10. `jshint-stylish`: полностью опционально, но эта задача преобразовывает
вывод `grunt-contrib-jshint` в отличный вид.
11. `grunt-contrib-watch`: запускает задачи при каких-либо изменениях
в наблюдаемых файлах.


## Конфигурация задач

Один из лучших модулей из тех, что я встречал,— модуль `load-grunt-config`.
Он позволяет нам задавать конфигурацию для каждой задачи в
отдельных файлах — это удобнее, чем просто складывать всё в один длинный
Gruntfile.

В директории `grunt` создайте следующие файлы:

    grunt/aliases.yaml
    grunt/concurrent.js
    grunt/clean.js
    grunt/imagemin.js
    grunt/jshint.js
    grunt/sass.js
    grunt/uglify.js
    grunt/watch.js

**Обратите внимание: имена этих файлов должны соответствовать названиям задач.**

Скопируйте и вставьте конфигурацию для каждой задачи в соответствующий файл.


### aliases.yaml

    default:
     description: 'Сборка по умолчанию (продакшн)'
     tasks:
      - prod
    dev:
     description: 'Сборка для разработки'
     tasks:
      - 'concurrent:devFirst'
      - 'concurrent:devSecond'
    img:
     description: 'Работа с изображениями'
     tasks:
      - 'concurrent:imgFirst'
    devimg:
     description: 'Сборка для разработки и работа с изображениями'
     tasks:
      - dev
      - img
    prod:
     description: 'Сборка продакшна'
     tasks:
      - 'concurrent:prodFirst'
      - 'concurrent:prodSecond'
      - img

Здесь мы задаём псевдонимы для наших задач:

*  `default` — запускает `prod` задачи, когда вы запускаете `grunt` из
командной строки.
*  `dev` — запускает задачи разработки (но не включает задачи по изображениям)
*  `img` — запускает задачи по изображениям
*  `devimg` — запускает задачи по разработке и изображениям
*  `prod` — запускает `prod` задачи и задачи по изображениям

**[Здесь][14] более подробная информация по настройке алиасов для
`load-grunt-config`.**


### concurrent.js

    module.exports = {
     
      // Настройки задач
      options: {
        limit: 3
      },
     
      // Задачи для разработки
      devFirst: [
        'clean',
        'jshint'
      ],
      devSecond: [
        'sass:dev',
        'uglify'
      ],
     
      // Задачи для продакшна
      prodFirst: [
        'clean',
        'jshint'
      ],
      prodSecond: [
        'sass:prod',
        'uglify'
      ],
     
      // Image tasks
      imgFirst: [
        'imagemin'
      ]
    };

Если, например, взять `dev`-задачи, вы увидите, что сначала должен запуститься
`clean`, а затем `sass:dev` и `uglify` одновременно — для перегенерации CSS и
Javascript.

**[Здесь][15] более подробная информация по управлению `grunt-concurrent`.**


### clean.js

    module.exports = {
      all: [
        "dist/"
      ]
    };

Управлять `grunt-contrib-clean` совсем несложно: здесь я просто говорю, что
нужно удалить содержимое директории `dist/`. Используйте эту задачу с
осторожностью — она будет удалять без разбора всё, что вы ей скажете — без
всяких оповещений, поэтому убедитесь, что всё настроили правильно.

**[Здесь][16] более подробная информация по конфигурированию
`grunt-contrib-clean`.**


### imagemin.js

    module.exports = {
      all: {
        files: [{
          expand: true,
          cwd: 'src/',
          src: ['images/*.{png,jpg,gif}'],
          dest: 'dist/'
        }]
      }
    };

Эта задача просто оптимизирует все изображения из `src/images/` и сохраняет их
в `dist/images/`.

**[Здесь][17] более подробная информация по управлению
`grunt-contrib-imagemin`.**


### sass.js

    module.exports = {
      // Настройки для разработки
      dev: {
        options: {
          outputStyle: 'nested',
          sourceMap: true
        },
        files: [{
          expand: true,
          cwd: 'src/styles',
          src: ['*.scss'],
          dest: 'dist/styles',
          ext: '.css'
        }]
      },
      // Настройки для продакшна
      prod: {
        options: {
          outputStyle: 'compressed',
          sourceMap: false
        },
        files: [{
          expand: true,
          cwd: 'src/styles',
          src: ['*.scss'],
          dest: 'dist/styles',
          ext: '.css'
        }]
      }
    };

Я разделил Sass-задачу на процессы для разработки (`dev`) и для выкладки на
действующий сайт (`prod`). Конфигурации очень схожи, но для разработки я
установил стиль вывода `nested` и включил карты кода.

**[Здесь][18] более подробная информация по конфигурации `grunt-sass`.**


### jshint.js

    module.exports = {
     
      options: {
        reporter: require('jshint-stylish')
      },
     
      main: [
        'src/scripts/*.js'
      ]
    };

Задача `jshint` проверяет Javascript и сообщает, всё ли в порядке.

**[Здесь][19] более подробная информациия по конфигурированию
`grunt-contrib-jshint`.**


### uglify.js

    module.exports = {
      all: {
        files: [{
          expand: true,
          cwd: 'src/scripts',
          src: '**/*.js',
          dest: 'dist/scripts',
          ext: '.min.js'
        }]
      }
    };

Задача `uglify` берёт Javascript-файлы и минифицирует их — всё просто!

**[Здесь][20]
более подробная информация по конфигурированию `grunt-contrib-uglify`.**


### watch.js

    module.exports = {
     
      options: {
        spawn: false,
        livereload: true
      },
     
      scripts: {
        files: [
          'src/scripts/*.js'
        ],
        tasks: [
          'jshint',
          'uglify'
        ]
      },
     
      styles: {
        files: [
          'src/styles/*.scss'
        ],
        tasks: [
          'sass:dev'
        ]
      },
    };

`watch` запускает заданные задач при изменении в наблюдаемых файлах — при
добавлении, редактировании или удалении.

**Обратите внимание: наиболее простой способ заставить работать Livereload —
это установить [расширение для браузера][21].**

**[Здесь][22] более подробная информация по конфигурированию
`grunt-contrib-watch` и Livereload.**


## Запуск задач

Если вы уже закончили создавать свой проект по инструкции выше,
вы сможете запустить выполнение задач. Как уже говорилось, есть различные
псевдонимы для задач, которые вы можете запускать. Сейчас просто запустите
`grunt` в командной строке из корня вашего проекта.

Если всё в порядке, вы должны увидеть ползущий по экрану текст и финальное
сообщение, примерно такое:

![Вывод продакшн-сборки Grunt][Вывод продакшн-сборки Grunt]

Мне нравится небольшой отчет, который выводит `time-grunt`. Я могу увидеть
сколько времени выполняются задачи, запускаемые одновременно, и сколько
занимает весь процесс — круто!

В зависимости от ваших требований вы также можете выбрать для запуска команды
`grunt dev`, `grunt devimg` или `grunt img`.

Ещё вы можете запустить `grunt watch`, если вам нужно следить за изменениями
в ваших `.scss` и `.js` файлах и автоматически запускать выполнение `sass`
или `jshint`
и `uglify`.


## Выводы

Ну вот, пожалуй, и всё. Если вы поэкспериментируете с примерами, описанными
выше, то скоро набьёте руку, начнёте добавлять новые задачи и подстраивать
рабочий процесс под свои нужды.

**Повторюсь, код из этой статьи можно найти на [Github][4].**

Оставляйте любые вопросы в комментариях ниже или задавайте их на [github][23].

 [Логотип Grunt]: img/tumblr_inline_mjrobcZQpo1qz4rgp.png "Логотип Grunt"

 [2]: http://mattbailey.io/a-beginners-guide-to-grunt/ "Matt Bailey: A Beginner's Guide To Grunt"
 [3]: http://gruntjs.com/ "Grunt"
 [4]: https://github.com/matt-bailey/grunt-frontend-boilerplate
 [5]: http://nodejs.org/download/
 [6]: http://gruntjs.com/getting-started
 [7]: http://iterm2.com/
 [8]: http://libsass.org/
 [9]: https://www.ruby-lang.org/en/installation/
 [10]: http://sass-lang.com/download.html
 [11]: http://yeoman.io/
 [12]: https://github.com/shootaroo/jit-grunt
 [13]: https://github.com/gruntjs/grunt-contrib-sass
 [14]: https://github.com/firstandthird/load-grunt-config#aliases
 [15]: https://github.com/sindresorhus/grunt-concurrent
 [16]: https://github.com/gruntjs/grunt-contrib-clean
 [17]: https://github.com/gruntjs/grunt-contrib-imagemin
 [18]: https://github.com/sindresorhus/grunt-sass
 [19]: https://github.com/gruntjs/grunt-contrib-jshint
 [20]: https://github.com/gruntjs/grunt-contrib-uglify
 [21]: http://feedback.livereload.com/knowledgebase/articles/86242
 [22]: https://github.com/gruntjs/grunt-contrib-watch
 [Вывод продакшн-сборки Grunt]: img/grunt-frontend-boilerplate-1.png "Вывод продакшн-сборки Grunt"
 [23]: https://github.com/matt-bailey/grunt-frontend-boilerplate/issues
