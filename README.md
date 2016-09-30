# gulp-recipes
> A collection of some useful gulp tasks.

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
