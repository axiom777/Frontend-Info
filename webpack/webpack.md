# Webpack
Вы уже посмотрели [полезные видео](https://github.com/axiom777/Frontend-Info/blob/master/usefull.md#%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-%D0%B2%D0%B5%D0%B1%D0%BF%D0%B0%D0%BA) по настройке Webpack, но настал этап поключения различных препроцессоров.

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
1. Для минификации используется свозйство _pretty_ по умолчанию оно находится в состоянии _false_ поэтому весь код не минифицирован
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

   Если вы внимательно читали [документацию по sass-loader](https://webpack.js.org/loaders/sass-loader/), то там есть раздел [Problems with url(...)](https://webpack.js.org/loaders/sass-loader/#problems-with-url) - суть его в том, что при импортах в scss @import - пути относительно импортированных внутри scss файлов работать не будут и вы должны писать все пути относительно того файла который импортируете в точку входа. Если вас такой вариант не устраивает - используйте [resolve-url-loader](https://github.com/bholloway/resolve-url-loader).
   
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
   
   
