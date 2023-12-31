# My Gulp Build

This is a comprehensive Gulp build configuration designed to optimize the web development workflow. It automates various tasks such as SCSS to CSS compilation, file optimization, and browser syncing during development. With this build, you can focus on writing code rather than repetitive tasks.

## Features

- **SCSS to CSS Compilation**: Write styles in SCSS format, and Gulp handles the compilation process, generating deployable CSS files.

- **CSS Minification**: Compiled CSS files are automatically minified to reduce file size and improve website performance.

- **JavaScript Handling**: JavaScript files are moved to the appropriate build folder, making script organization and management easier.

- **HTML Minification**: HTML files are minified, removing unnecessary white spaces and reducing file size.

- **Image Optimization**: Images are automatically optimized to reduce their size without sacrificing quality, resulting in faster page load times.

- **Browser Auto-Refresh**: During development, any changes made to source files trigger an automatic browser refresh, allowing you to see updates instantly.

## Getting Started

To use this Gulp build in your project, follow these steps:

1. **Clone the Repository**: Start by cloning this repository to your local machine using the command `git clone <repository-url>`.

2. **Install Dependencies**: Navigate to the project folder and install the required dependencies by running `npm install`.

3. **Project Configuration**: Modify the project structure by modifying the source files located in the `src` folder. Add or remove tasks in the `gulpfile.js` file to customize the build process according to your specific needs.

4. **Build the Project**: Run the command `gulp` in the project's root folder. This will initiate the build process, including SCSS compilation, file optimization, and generating deployable files in the `dist` folder.

5. **Start the Development Server**: A local development server will be launched, and you can access your project at `http://localhost:3000` in your browser. Any changes made to the source files will automatically trigger a browser refresh, allowing you to see the updates instantly.

Feel free to explore and customize the Gulp tasks and configuration to fit your project's requirements. Enjoy a more efficient and productive web development experience with this Gulp build!

**Note**: Make sure you have Node.js and Gulp installed before using this build configuration.

## Sample gulpfile.js
This file will give you a taste of what gulp does.
```javascript
const gulp = require('gulp');
const sass = require('gulp-sass')(require('sass'));
const cleanCSS = require('gulp-clean-css');
const htmlmin = require('gulp-htmlmin');
const browserSync = require('browser-sync').create();
const fs = require('fs');
const path = require('path');

// The paths object contains the paths to source and destination files and folders
const paths = {
    styles: {
        src: 'src/scss/**/*.scss', // Path to SCSS source files
        dest: 'dist/css' // Path to the target folder for compiled CSS
    },
    html: {
        src: 'src/*.html', // Path to HTML source files
        dest: 'dist' // Path to the target folder for HTML files
    },
    js: {
        src: 'src/js/**/*.js', // Path to JavaScript source files
        dest: 'dist/js' // Path to the target folder for JavaScript files
    },
    images: {
        src: 'src/images/**/*', // Path to source images
        dest: 'dist/images' // Path to the target folder for images
    },
    fonts: {
        src: 'src/fonts/**/*', // Path to font source files
        dest: 'dist/fonts' // Path to the target folder for fonts
    }
};

// Function to clean the target folders before building
function clean() {
    return new Promise((resolve, reject) => {
        if (fs.existsSync(paths.styles.dest)) {
            const files = fs.readdirSync(paths.styles.dest);
            for (const file of files) {
                if (file !== 'style.scss') {
                    fs.unlinkSync(path.join(paths.styles.dest, file));
                }
            }
        }
        resolve();
    });
}

// Function to create the target folders before building
function createDistFolder() {
    return new Promise((resolve, reject) => {
        fs.mkdir(paths.styles.dest, { recursive: true }, err => {
            if (err) reject(err);
            else {
                fs.mkdir(paths.js.dest, { recursive: true }, err => {
                    if (err) reject(err);
                    else {
                        fs.mkdir(paths.images.dest, { recursive: true }, err => {
                            if (err) reject(err);
                            else {
                                fs.mkdir(paths.fonts.dest, { recursive: true }, err => {
                                    if (err) reject(err);
                                    else {
                                        resolve();
                                    }
                                });
                            }
                        });
                    }
                });
            }
        });
    });
}

// Function to compile and minify SCSS styles
function styles() {
    return gulp
        .src(paths.styles.src)
        .pipe(sass().on('error', sass.logError))
        .pipe(cleanCSS())
        .pipe(gulp.dest(paths.styles.dest))
        .pipe(browserSync.stream());
}

// Function to minify HTML
function html() {
    return gulp
        .src(paths.html.src)
        .pipe(htmlmin({ collapseWhitespace: true }))
        .pipe(gulp.dest(paths.html.dest))
        .pipe(browserSync.stream());
}

// Function to move JavaScript files
function scripts() {
    return gulp
        .src(paths.js.src)
        .pipe(gulp.dest(paths.js.dest))
        .pipe(browserSync.stream());
}

// Function to move index.html file to the target folder
function moveIndex() {
    return gulp
        .src('src/index.html', { allowEmpty: true })
        .pipe(gulp.dest(paths.html.dest))
        .pipe(browserSync.stream());
}

// Function to move main.js file to the target folder
function moveMainJs() {
    return gulp
        .src('src/js/main.js', { allowEmpty: true })
        .pipe(gulp.dest(paths.js.dest))
        .pipe(browserSync.stream());
}

// Function to copy styles from src/css to dist/css
function copyStyles() {
    return gulp
        .src('src/css/**/*.css')
        .pipe(gulp.dest('dist/css'))
        .pipe(browserSync.stream());
}

// Function to start a development server and watch for file changes
function serve() {
    browserSync.init({
        server: {
            baseDir: './dist'
        }
    });

    gulp.watch('src/**/*', gulp.series(clean, gulp.parallel(styles, html, scripts, moveIndex, moveMainJs, copyStyles))).on('change', browserSync.reload);
}

// Export tasks for command line access
exports.clean = clean;
exports.styles = styles;
exports.html = html;
exports.scripts = scripts;
exports.moveIndex = moveIndex;
exports.moveMainJs = moveMainJs;
exports.serve = serve;
exports.default = gulp.series(
    createDistFolder,
    clean,
    gulp.parallel(styles, html, scripts, moveIndex, moveMainJs),
    serve
);
```


## Folder Structure
Here is the complete folder structure recommended to be used with this Gulp build:

```
- dist/               # Build folder
  - css/              # Compiled CSS files
  - js/               # JavaScript files
  - images/           # Optimized images
  - index.html        # Build HTML file
- src/                # Source files
  - js/               # JavaScript files
  - scss/             # SCSS files
    - styles.scss     # Main SCSS file
    - abstracts/      # Abstractions folder
      - _variables.scss      # Variables
      - _functions.scss      # Functions
      - _mixins.scss         # Mixins
      - _placeholders.scss   # Placeholders
    - base/           # Base styles folder
      - _reset.scss           # Reset styles
      - _typography.scss      # Typography
      - _utilities.scss       # Utility classes
    - components/     # Components folder
      - _buttons.scss         # Button styles
      - _carousel.scss        # Carousel styles
      - _modal.scss           # Modal styles
    - layout/         # Layout components folder
      - _header.scss          # Header styles
      - _footer.scss          # Footer styles
      - _navigation.scss      # Navigation styles
    - pages/          # Page styles folder
      - _home.scss            # Styles for the "Home" page
      - _about.scss           # Styles for the "About" page
    - custom.scss     # File for your custom styles
- gulpfile.js         # Gulp task configuration
- package.json        # Project dependencies and scripts
- README.md           # Project documentation

```

- `dist/`: This folder contains the deployable project files after the build process.
- `dist/css/`: Minified CSS file is located here.
- `dist/js/`: Minified JavaScript file is located here.
- `dist/images/`: Optimized images are stored here.
- `dist/index.html`: Final minified HTML file.

- `src/`: This folder contains the source files of the project.
- `src/scss/`: SCSS files are located here.
- `src/js/`: JavaScript files are located here.
- `src/images/`: Raw images are stored here.
- `src/index.html`: Source HTML file of the project.

- `

gulpfile.js`: Gulp configuration file containing tasks for building the project.
- `package.json`: File listing project dependencies and scripts.
- `README.md`: File containing project description and usage instructions.

## Installed Packages

The following packages were installed in the project:

- `gulp`: Gulp.js - a task runner for automating development workflows.
- `gulp-sass`: Plugin for compiling SCSS to CSS.
- `gulp-clean-css`: Plugin for minifying CSS files.
- `gulp-uglify`: Plugin for minifying JavaScript files.
- `gulp-htmlmin`: Plugin for minifying HTML files.
- `gulp-imagemin`: Plugin for optimizing images.
- `browser-sync`: Tool for automatic browser reloading on file changes.
- `del`: Plugin for deleting files and directories.
- `gulp-concat`: Plugin for file concatenation.
- `gulp-rename`: Plugin for file renaming.

These packages will help automate various build and optimization tasks in the project.

## Questions and Support

If you have any questions or need assistance, feel free to reach out to us. We're happy to help you with the setup and usage of this Gulp build.

## Contributions

If you have any suggestions for improving this Gulp build or have found any issues, please open a pull request or create an issue in the repository's "Issues" section. We welcome your contributions and strive to make this Gulp build even better.

## License

This project is licensed under the MIT License. See the `LICENSE` file for more information.

[gulp]: https://gulpjs.com/
[scss]: https://sass-lang.com/
[mit-license]: https://opensource.org/licenses/MIT
