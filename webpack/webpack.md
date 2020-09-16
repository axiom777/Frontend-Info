# Webpack
Вы уже посмотрели [полезные видео](https://github.com/axiom777/Frontend-Info/blob/master/usefull.md#%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-%D0%B2%D0%B5%D0%B1%D0%BF%D0%B0%D0%BA) по настройке Webpack, но настал этап поключения различных препроцессоров

## Подключение PUG
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
