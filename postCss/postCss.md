# postCss
PostCSS是基于Javascript的，我们在项目中可以使用像Gulp和Grunt这样的构建工具去转化CSS。

## grunt环境安装
``` bash
npm i grunt-postcss -D
```

### 配置Gruntfile.js
``` js
module.exports = function(grunt) {
  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    styles: {
      options: {
        processors: []
      },
      dist: {
        files: [{
          expand: true,
          cwd: 'dev/',
          src: ['**/*.css'],
          dest: 'prod/'
        }]
      }
    }
  });
  // Load post-css.
  grunt.loadNpmTasks('grunt-postcss');
};
```

## gulp环境安装
``` bash
npm i gulp-postcss -D
```

### 配置Gulpfile.js
``` js
var postcss = require('gulp-postcss');
gulp.task('styles', function () {
  return gulp.src('path/to/dev/style.css')
    .pipe(postcss([]))
    .pipe(gulp.dest(path/to/prod))
});
```

## PostCSS插件
``` bash
# 用来给每个鼠标悬浮状态添加一个:focus
npm i postcss-focus -D

# 自动添加前缀
npm i autoprefixer -D

# 使用未来的语法
npm i cssnext -D
```