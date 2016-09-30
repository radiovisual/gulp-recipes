# gulp-recipes
> A collection of some useful gulp tasks.

### Concatenate/minify JavaScript dependencies

```js
var uglify = require('gulp-uglify');

gulp.task('build-deps', function() {
    console.log('building dependencies to file: ./src/js/vendor/build/dependencies.min.js');
    return gulp.src(['./dist/js/vendor/*.js'])
        .pipe(uglify({
            preserveComments: 'license'
        }))
        .pipe(concat('dependencies.min.js'))
        .pipe(gulp.dest('./dist/js/vendor/build/'));
});
```

### Reload browser when dependencies have changed

```js
var browserSync = require('browser-sync').create();

gulp.task('browser-sync', function () {
    browserSync.init({
        server: {
            baseDir: './dist'
        }
    });
    gulp.watch(['./dist/*.html'], ['reload']);
    gulp.watch(['./dist/css/*.css'], ['reload']);
    gulp.watch(['./dist/js/*.js'], ['reload']);
});

gulp.task('reload', function () {
    browserSync.reload();
});
```

### Upload project files via FTP

```js
var ftp = require( 'vinyl-ftp' );

gulp.task('ftp', function () {
    var conn = ftp.create({
        host:     process.env.FTP_HOST,
        user:     process.env.FTP_USER,
        password: process.env.FTP_PASS,
        parallel: 2,
        log:      gutil.log
    });
    var globs = [
        'dist/css/**/*',
        'dist/js/**/*.js',
        'dist/img/**/*',
        'dist/.htaccess'
    ];
    // using base = '.' will transfer everything to /public_html correctly
    // turn off buffering in gulp.src for best performance
    return gulp.src(globs, {base: '.', buffer: false})
        .pipe(conn.newer( '/' )) // only upload newer files
        .pipe(conn.dest( '/' ));
});
```

### Convert SASS to CSS

```js
var uglifycss = require('gulp-uglifycss');
var concat = require('gulp-concat');
var sass = require('gulp-sass');

gulp.task('sass', function () {
    return gulp.src(['./dist/sass/*.scss', '!./dist/sass/mixins.scss', '!./dist/sass/print.scss'])
        .pipe(sass().on('error', sass.logError))
        .pipe(uglifycss({
            "maxLineLen": 80,
            "uglyComments": true
        }))
        .pipe(concat('styles.min.css'))
        .pipe(gulp.dest('./dist/css'));
});
```

### Convert all markdown files to html

```js
var gulp = require('gulp');
var wrapper = require('gulp-wrapper');
var markdown = require('gulp-markdown');

var html = {
  header: '<html>',
  footer: '</html>'
}

gulp.task('build-html', function () {
  return gulp.src(files)
        .pipe(markdown({gfm: true}))
        .pipe(wrapper({
            header: html.header,
            footer: html.footer
        }))
        .pipe(gulp.dest('build/html'));
});
```


### Convert all markdown files to pdf

```js
var gulp = require('gulp');
var markdownpdf = require('gulp-markdown-pdf');

gulp.task('build-pdfs', ['build-html'], function () {
    return gulp.src(files)
        .pipe(markdownpdf({
            cssPath: 'dependencies/pdf.css'
        }))
        .pipe(gulp.dest('build/pdf'));
});
```


### Concatenate all markdown files into a single html file

```js
var concat = require('gulp-concat');
var markdown = require('gulp-markdown');
var wrapper = require('gulp-wrapper');

gulp.task('concat-html', function() {
    return gulp.src(files)
        .pipe(concat('00-all-documents.md'))
        .pipe(markdown({gfm: true}))
        .pipe(wrapper({
            header: html.header,
            footer: html.footer
        }))
        .pipe(gulp.dest('build/html'));
});
```


### Concatenate all markdown files into a single pdf file

```js
var concat = require('gulp-concat');
var markdownpdf = require('gulp-markdown-pdf');

gulp.task('concat-pdf', function() {
    return gulp.src('*.md')
        .pipe(concat('00-all-documents.md'))
        .pipe(markdownpdf({
            cssPath: 'dependencies/pdf.css'
        }))
        .pipe(gulp.dest('build/pdf'));
});
```

### Copy folder from one location to another

```js
gulp.task('copy-dependencies', function() {
    return gulp.src(['dependencies/**/*']).pipe(gulp.dest('build/html/dependencies'));
});
```
