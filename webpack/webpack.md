# Webpack
Вы уже посмотрели [полезные видео](https://github.com/axiom777/Frontend-Info/blob/master/usefull.md#%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-%D0%B2%D0%B5%D0%B1%D0%BF%D0%B0%D0%BA) по настройке Webpack, но настал этап поключения различных препроцессоров.
## Оглавление
1. [Подключение PUG в Webpack](https://github.com/axiom777/Frontend-Info/blob/master/webpack/webpack.md#%D0%BF%D0%BE%D0%B4%D0%BA%D0%BB%D1%8E%D1%87%D0%B5%D0%BD%D0%B8%D0%B5-pug-%D0%B2-webpack)
1. [Подключение SASS / SCSS в Webpack](https://github.com/axiom777/Frontend-Info/blob/master/webpack/webpack.md#%D0%BF%D0%BE%D0%B4%D0%BA%D0%BB%D1%8E%D1%87%D0%B5%D0%BD%D0%B8%D0%B5-sass--scss-%D0%B2-webpack)
1. [File-loader](https://github.com/axiom777/Frontend-Info/blob/master/webpack/webpack.md#file-loader)
1. [webpack-dev-server](https://github.com/axiom777/Frontend-Info/blob/master/webpack/webpack.md#webpack-dev-server)
1. [Структура каталогов проекта](https://github.com/axiom777/Frontend-Info/blob/master/webpack/webpack.md#%D1%81%D1%82%D1%80%D1%83%D0%BA%D1%82%D1%83%D1%80%D0%B0-%D0%BF%D1%80%D0%BE%D0%B5%D0%BA%D1%82%D0%B0)
1. [Просто полезные плагины](https://github.com/axiom777/Frontend-Info/blob/master/webpack/webpack.md#%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BF%D0%BB%D0%B0%D0%B3%D0%B8%D0%BD%D1%8B)

Планируется когда нибудь добавить:

1. Как не добавлять каждый файл pug в проект, пусть они все сразу добавляются автоматом
1 .Разбиение на чанки [chunks]
1. Настройка post-css и всяких, автопрефиксеров.
1. Может быть про package.json и как работать с env + там всякие cross-env.
1. Заливка проекта на github, настройка автоматического деплоя плагины gh-pages и что то там еще было
1. Как поменять пути при деплое на нужные в publicPath



## Подключение PUG в Webpack
1. Загрузить pug и pug-loader
   ```
   npm install -d pug pug-loader
   ```
1. Если не установлен __html-webpack-plugin__, также его устанавливаем
   ```
   npm install -d html-webpack-plugin
   ```
1. Подключаем в сборке Webpack
   Добавляем в ваш _webpack.config.js_ следующие конструкции, если их нет
   ```
   const path = require('path'); // Нужен для получения путей
   const HtmlWebpackPlugin = require('html-webpack-plugin')
   
   
   module.exports = {
     entry: 'index.js',
     output: {
       path: path.resolve(__dirname, './dist'),
       filename: 'index_bundle.js'
     },
     module: {
       rules: [
         {
            test: /\.pug$/,
            loader: 'pug-loader',
         },
       ],
     },
     plugins: [
      new HtmlWebpackPlugin({
        filename: `index.html`, // файл который вы получите на выходе
        template: `${path.resolve(__dirname, '.')}src/index.pug`, // шаблон откуда будет взять файл pug
      }),
   };
   ```
   Если проект подразумевает несколько страниц, то для каждой новой страницы добавляется новый блок __new HtmlWebpackPlugin__ где указываете свои пути.
   В принципе проект уже должен начать собираться, и на выходе должна получаться html страница из pug кода.
   
### Дополнительные настройки pug-loader
Все возможности данного лоадера описаны на его странице https://github.com/pugjs/pug-loader - разберем их:
1. Для минификации используется свойство _pretty_ по умолчанию оно находится в состоянии _false_ поэтому весь код минифицирован
1. Пути в проекте и __root__ - Как написано на [странице документации](https://github.com/pugjs/pug-loader), лоадер использует свои пути для нахождения других файлов, поэтому ,например _alias_, который используется в том же SCSS, здесь работать не будет. Для этого используется собственный механизм __root__, который позволяет указывать абсолютные пути в проекте, для подключения других блоков.
1. Подключение вышеописанного:
   ```
   {
     test: /\.pug$/,
     loader: 'pug-loader',
     options: {
       pretty: true, // Код теперь не минифицирван
       root: path.resolve(__dirname, 'src'), // Можно прописывать абсолютные пути внутри pug относительно указанного пути.
     },
   },
   ```
1. __N.B. Когда в проекте pug будете подключать картинки их следует добавлять через require:__
   ```
   div
     img(src=require("./my/image.png"))
   ```
   В противном случае Webpack не будет подхватывать даные изображения и file-loader не перенесет изображения в проект.
Если появится дополнительная информация, что можно еще написать про pug-loader - дополним)))

## Подключение SASS / SCSS в Webpack
Как же писать без препроцессоров? Да лекго!, но с ними удобнее. Подключение:

1. Обращаемся к [документации](https://webpack.js.org/loaders/sass-loader/). Обратите внимание, там предлагают использовать _Dart Sass_, я его не пробовал, кто хочет эксперементируйте, поэтому подем простым путём:
   ```
   npm install sass-loader sass webpack --save-dev
   ```
   Если у вы до этого не настраивали проект для работы с css, данные лоадеры также необходимо будет скачать:
   ```
   npm install style-loader css-loader --save-dev
   ```

1. Подключение в вебпак и базовое конфигурирование:
   ```
   module.exports = {
     module: {
       rules: [
         {
           test: /\.s[ac]ss$/i,
           use: [
             'style-loader',
             'css-loader',
             'sass-loader',
           ],
         },
       ],
     },
   };
   ```
   Таким образом, стили написанные в scss / sass будут в проекте, но отдельные файлы css в итоговой сборке не появятся - про это позже.
   Чтобы протестировать, необходимо scss файл импортировать в js - точки входа - это тот - который описывается в самом верху например entry: "index.js"

1. Проблемы с путями при имортах:

   Если вы внимательно читали [документацию по sass-loader](https://webpack.js.org/loaders/sass-loader/), то там есть раздел [Problems with url(...)](https://webpack.js.org/loaders/sass-loader/#problems-with-url) - суть его в том, что при импортах в scss @import - пути относительно импортированных внутри scss файлов работать будут не корректно и вы должны писать все пути относительно того файла который импортируете в точку входа (либо все scss импортировать в точку входа). Если вас такой вариант не устраивает - используйте [resolve-url-loader](https://github.com/bholloway/resolve-url-loader).
   
   Если не использовать resolve-url-loader - также возникает проблема при подключении различных сторонних библиотек, где используется scss, по вышеуказанной причине.
   
   В докуентации указано, чтобы данный лоадер корректно работал, необходимо использовать _source-map_.
   
   Установка:
   ```
   npm install resolve-url-loader --save-dev
   ```
   
   Изменение конфигурации:
   ```
   module.exports = {
     module: {
       rules: [
         {
           test: /\.s[ac]ss$/i,
          use: [
            {
              loader: 'css-loader',
            },
            {
              loader: 'resolve-url-loader',
            },
            {
              loader: 'sass-loader',
              options: {
                sourceMap: true,
              },
            },
          ],
         },
       ],
     },
   };
   ```

В принципе базовая настройка SCSS закончена.

### SCSS - экспорт стилей в файл css
В вышеописанной сборке стили будут подключаться в js, чтобы их вынести в отдельные файлы - воспользуемся [mini-css-extract-plugin](https://www.npmjs.com/package/mini-css-extract-plugin). Если вы ранее установили по урокам extract-text-webpack-plugin - заменяем, так как он плохо работает на последних версиях и официальная документация рекомендует использовать mini-css-extract-plugin - можете [проверить](https://www.npmjs.com/package/extract-text-webpack-plugin).

Установка:
```
npm install --save-dev mini-css-extract-plugin
```

Настройка Webpack:
```
const MiniCssExtractPlugin = require('mini-css-extract-plugin'); // Импортируем!!!

   module.exports = {
     module: {
       rules: [
         {
           test: /\.s[ac]ss$/i,
          use: [
            { // Добавляем еще 1 лоадер
              loader: MiniCssExtractPlugin.loader,
            },
            {
              loader: 'css-loader',
            },
            {
              loader: 'resolve-url-loader',
            },
            {
              loader: 'sass-loader',
              options: {
                sourceMap: true,
              },
            },
          ],
         },
       ],
       plugins: [
          new MiniCssExtractPlugin({
           filename: '[name].css', // Тут также можно указать пути 'assets/css/[name].css'. 
         }),                       // Такая конструкция положит css файл в корень dist
       ],                          // name Берется из названии точки входа.
     },                            // Если название не указано, по умолчанию она называется main
   };

```
После добавления таких настроек - на выходе должен формироваться css файл.

## File-loader
Без него к сожалению не обойтись((( Используется для загрузки изображений, шрифтов, и может еще чего-то там.
В pug если вы писали через require - он обработает картинки и пути к ним
Из scss он достанет изображения - которые были, в _url()_ - например в конструкции background: url('./bg.jpg'); или в подключаемых шрифтах.
[Официальная документация на file-loader](https://webpack.js.org/loaders/file-loader/)

Установка:
```
npm install file-loader --save-dev
```

Подключение скину сразу свою конфигурацию с комментариями:
```
    rules: [
      {
          test: /\.(woff(2)?|ttf|eot|svg)$/, // Регуряркой указаны все расширения, которые используются
          include: [
            path.resolve(__dirname, 'src/fonts'),    // include - будет брать только из данных каталогов
            path.resolve(__dirname, 'node_modules'), // Для подключения шрифтов из пакетов если на них есть ссылка
          ],
          use: {
            loader: 'file-loader',
            options: {
              name: '[name].[ext]',
              outputPath: 'assets/fonts',
            },
          },
        },
        {
          test: /\.(png|jpg|jpeg|svg|gif)$/,
          exclude: [path.resolve(__dirname, 'src/fonts')], // exclude не разрешает смотреть в каталог шрифтов
          use: {                                           // требуется это, так как svg есть в шрифтах и картинках
            loader: 'file-loader',                         // и необходимо файлы шрифтов класть в один каталог, а
            options: {                                     // изображений в другой.
              name: '[name].[ext]',
              outputPath: 'assets/images',
              // publicPath: '/assets/images', // publicPath - может понадобится в финале разработки, но на первых 
            },                                 // порах его можно не использовать.
          },
        },
    ],
```
Если с exclude и include всё более или менее понятно, то с _outputPath_ немного сложнее.

### outputPath
_Во первых относительно чего стоятся пути?_
Как я понимаю, при простой настройке, где указывается _entry_ и _output_, в _output_ есть множество дополнительных [настроек](https://webpack.js.org/configuration/). Пути стоятся на выходе относительно __path:__, поэтому это и учитываем. Бывает ошибка - например, если вы хотите сложить файлы js в определенный каталог, __не следует писать этот путь в path__, а просто добавляем путь к __filename: 'assets/js/[name].js'__,

В общем _outputPath_ положит все файлы в указанные каталоги относительно, _path_ который находится в _output:_ > _path:_ конфигурации.

### publicPath для file-loader
Если проект большой и файлы проекта разбросаны по различным каталогам, то бывают различные проблемы с путями. Во-первых они прописываются все как относительные и часто бывает проблема, что непонятно относительного чего они прописаны.

_publicPath_ позволяет гвоздями вбить пути на картинки и шрифты. Все ссылки на изображения и шрифты будут иметь данный путь. Особенно удобно, если использовать абсолютные пути _publicPath_, да будут проблемы если вы будете открывать файлы html без dev сервера. Но вообще, работу тут надо проводить с dev-server.

## webpack-dev-server
Нужная и полезная штука [плагин](https://github.com/webpack/webpack-dev-server).
[Документация на сайте webpack](https://webpack.js.org/configuration/dev-server/)

Установка:
```
npm install webpack-dev-server --save-dev
```
Конфигурация - моя:
```
module.exports = {
  //...
  devServer: {
    contentBase: path.join(__dirname, 'dist'), // Просто путь в каталог сборки
    compress: true,
    port: 9000 // Порт в браузёре
    overlay: {
      warnings: true, // Чтобы предупреждения выбешивали не только в консоле
      errors: true,   // и ошибки пусть также красочно бесят на страницах))))
    },
  }
};
```
Да тут в принципе нечего рассказывать.

## Структура проекта
Меня в принципе устраивает описание структуры предложенное FSD, как я её понимаю и делаю

Верстка будет стоиться на основе методологии [БЭМ](https://ru.bem.info/), где есть блоки. У FSD указано, что каждый блок должен находиться в своём каталоге. Для этого в src делаю каталог blocks - внутри blocks создается каталоги с названиями блоков - уже внутри них лежат файлы pug, scss, js - c таким же названием как у блока.

Приведу пример своей струтуры в src:

+ blocks _Тут хранятся все блоки_
   + header
      + images - _каталог для картинок, конкретного блока, может это и не правильно - я делаю пока так_
         + image.svg
      + header.scss
      + header.pug
      + header.js - _если используется для этого блока_
   + footer
      + footer.scss
      + и т.п.
+ pages _Каталог для страниц_
   + headers-footers
     + headers-footers.scss
     + headers-footers.pug
     + headers-footers.js
+ layouts - _каталог для шаблонов (layout) pug_
   + ui-kit.pug
   + main.pug
+ scss _Сюда я кладу стили, которые не относятся к блокам_, 
   + index.scss _например определние font-size для rem, box-sizing для всего_
   + variables.scss _различные переменные которые используются в проекте_
   + mixins.scss __
+ fonts
   + Каталоги с шрифтами
   + fonts.scss - _то, где подключаются шрифты. Можно было бы положить файл в каталог scss_
+ index.js - _файл который использую как точку входа. У меня в проекте только одна точка входа. Делать их под каждую страницу можно, но по мне это финальные приседания, так как если у вас не 1 - 5  страниц, а больше 15 - всё тормозит и без отдельных точек входа_

Как то так можно сделать структуру проекта.


## Полезные плагины
### Плагины
1. [clean-webpack-plugin](https://www.npmjs.com/package/clean-webpack-plugin) Чистит dist автоматически
1. [Генератор фавиконок для вебпак](https://github.com/jantimon/favicons-webpack-plugin) Без комментариев
1. [SCSS если не хочется везде импортировать файл с переменными](https://www.npmjs.com/package/sass-resources-loader) Народ пробовал работает. Найдено [тут](https://stackoverflow.com/questions/47491689/accessing-global-variables-in-sass-with-webpack-without-multiple-imports)
### Ссылки м.б полезные

