## gulp-uglify(JS压缩)
**安装**
```
npm install --save-dev gulp-uglify
```
**使用**
```
var gulp = require('gulp');  //加载gulp
var uglify = require('gulp-uglify');  //加载js压缩

// 定义一个任务 uglify
gulp.task('uglify', function () {
    gulp.src(['js/*.js','!js/*.min.js'])  //获取文件，同时过滤掉.min.js文件
        .pipe(uglify())
        .pipe(gulp.dest('javascript/'));  //输出文件
});
```

## gulp-minify-css（CSS压缩
**安装**
```
npm install --save-dev gulp-minify-css
```
**使用**
```
var gulp = require('gulp');
var minify = require('gulp-minify-css');

gulp.task('cssmini', function () {
    gulp.src(['css/*.css', '!css/*.min.css'])  //要压缩的css
        .pipe(minify())
        .pipe(gulp.dest('buildcss/'));
});
```

## gulp-minify-html（html压缩）
**安装**
```
npm install --save-dev gulp-minify-html
```
**使用**
```
var gulp = require('gulp');
var htmlmini = require('gulp-minify-html');

gulp.task('htmlmini', function () {
    gulp.src('*.html')
        .pipe(htmlmini())
        .pipe(gulp.dest('minihtml'));
})
```

## gulp-jshint（JS代码检查）
**安装**
```
npm install --save-dev gulp-jshint
```
**使用**
```
var gulp = require('gulp');
var jshint = require("gulp-jshint");

gulp.task('jsLint', function () {
    gulp.src('js/*.js')
    .pipe(jshint())
    .pipe(jshint.reporter()); // 输出检查结果
});
```

## gulp-concat（文件合并)
**安装**
```
npm install --save-dev gulp-concat
```
**使用**
```
var gulp = require('gulp');
var concat = require("gulp-concat");

gulp.task('concat', function () {
    gulp.src('js/*.js')  //要合并的文件
    .pipe(concat('all.js'))  // 合并匹配到的js文件并命名为 "all.js"
    .pipe(gulp.dest('dist/js'));
});
```

## gulp-less（编译Less）
**安装**
```
npm install --save-dev gulp-less
```
**使用**  
```
var gulp = require('gulp'),
    less = require("gulp-less");

gulp.task('compile-less', function () {
    gulp.src('less/*.less')
    .pipe(less())
    .pipe(gulp.dest('dist/css'));
});
```

## gulp-sass（编译Sass）
**安装**
```
npm install --save-dev gulp-sass
```
**使用**
```
var gulp = require('gulp'),
    sass = require("gulp-sass");

gulp.task('compile-sass', function () {
    gulp.src('sass/*.sass')
    .pipe(sass())
    .pipe(gulp.dest('dist/css'));
});
```

## gulp-livereload（自动刷新）
**安装**
```
npm install --save-dev gulp-livereload
```
**使用**

当代码变化时，它可以帮助我们自动刷新页面，该插件最好配合谷歌浏览器，且要安装livereload chrome extension扩展插件，否则无效。
```
var gulp = require('gulp'),
    less = require('gulp-less'),
    livereload = require('gulp-livereload');

gulp.task('less', function() {
  gulp.src('less/*.less')
    .pipe(less())
    .pipe(gulp.dest('css'))
    .pipe(livereload());
});

gulp.task('watch', function() {
  livereload.listen(); //要在这里调用listen()方法
  gulp.watch('less/*.less', ['less']);  //监听目录下的文件，若文件发生变化，则调用less任务。
});
```

