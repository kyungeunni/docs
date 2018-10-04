---
title: "API: build 프로퍼티"
description: Nuxt.js를 사용하면 당신의 웹 어플리케이션을 원하는대로 빌드할 수 있도록 webpack 설정을 사용자 정의할 수 있습니다.
---

# build 프로퍼티

> Nuxt.js를 사용하면 당신의 웹 어플리케이션을 원하는대로 빌드할 수 있도록 webpack 설정을 사용자 정의할 수 있습니다.

## analyze

> Nuxt.js는 [webpack-bundle-analyzer](https://github.com/webpack-contrib/webpack-bundle-analyzer)를 사용하여 번들을 시각화, 최적화 할 수 있도록 알려줍니다.

- 타입: `Boolean` or `Object`
- 기본값: `false`

만약 object라면 [여기](https://github.com/webpack-contrib/webpack-bundle-analyzer#options-for-plugin) 작성된 문서처럼 프로퍼티를 사용할 수 있습니다.

예제 (`nuxt.config.js`):
```js
export default {
  build: {
    analyze: true
    // or
    analyze: {
      analyzerMode: 'static'
    }
  }
}
```

<p class="Alert Alert--teal">**정보:** `nuxt build --analyze` 또는 `nuxt build -a` 명령어를 사용하여 당신의 어플리케이션을 빌드하거나 [http://localhost:8888](http://localhost:8888)로 시각화 하여 실행할 수 있습니다.</p>

## babel

> JS와 Vue 파일에 대한 Babel 설정을 사용자 정의 합니다.

- 타입: `Object`
- 기본값:
```js
{
  presets: ['vue-app']
}
```

예제 (`nuxt.config.js`):
```js
export default {
  build: {
    babel: {
      presets: ['es2015', 'stage-0']
    }
  }
}
```

## cache

- 타입: `Boolean`
- 기본값: `false`

> [uglifyjs-webpack-plugin ](https://github.com/webpack-contrib/uglifyjs-webpack-plugin#options) and [cache-loader](https://github.com/webpack-contrib/cache-loader#cache-loader)의 캐쉬 사용여부

## cssSourceMap

- 타입: `boolean`
- 기본값: dev 일때는 `true`, production 일때는 `false`

> CSS Source Map 지원을 사용여부

## devMiddleware

- 타입: `Object`

[webpack-dev-middleware](https://github.com/webpack/webpack-dev-middleware) 에서 사용가능한 옵션을 확인하세요.

## extend
> 클라이언트 및 서버 번들에 대한 webpack 구성을 수동으로 확장합니다.

- 타입: `Function`

확장은 서버 번들에서 한번, 클라이언트 번들에서 한번 총 두번 호출됩니다. 메서드의 인자는 아래와 같습니다:
1. Webpack 설정 object
2. 다음 키가 있는 object (모두 boolean): `isDev`, `isClient`, `isServer`, `loaders`.

예제 (`nuxt.config.js`):
```js
export default {
  build: {
    extend (config, { isClient }) {
      // 오직 클라이언트 번들 설정만 확장
      if (isClient) {
        config.devtool = '#source-map'
      }
    }
  }
}
```

기본 webpack 구성에 대해 더 알고싶다면 [webpack 디렉토리](https://github.com/nuxt/nuxt.js/tree/master/lib/builder/webpack) 를 확인해주세요.


### extend 내 로더
`loaders` 는 [build.loaders](#loaders) 와 같은 객체 구조를 가지고 있으므로, `extend` 내의 로더의 옵션을 변경할 수 있습니다.

예제 (`nuxt.config.js`):

```js
export default {
  build: {
    extend (config, { isClient, loaders: { vue } }) {
      // 오직 클라이언트 번들 설정만 확장
      if (isClient) {
        vue.transformAssetUrls.video = ['src', 'poster']
      }
    }
  }
}
```

## extractCSS

> Vue Server Renderer [guidelines](https://ssr.vuejs.org/en/css.html) 를 이용하여 Common CSS Extraction 사용가능하게 하기

- 타입: `Boolean`
- 기본값: `false`

`extract-text-webpack-plugin` 사용하면 메인쳥크에서 CSS를 발췌하여 별도의 CSS파일로 만들 수 있습니다. (템플릿에 자동으로 주입됨). 이는 파일이 개별로 캐쉬될 수 있게합니다. 이 방법은 많은 CSS파일들이 공유될 때 추천됩니다. 비동기 components안의 CSS는 자바스크립트 스트링으로 남아있을 것이며 vue-style-loader가 처리합니다.

## filenames

> 사용자가 정의한 번들 파일이름

- 타입: `Object`

- 기본값:

```js
{
  app: ({ isDev }) => isDev ? '[name].js' : '[chunkhash].js',
  chunk: ({ isDev }) => isDev ? '[name].js' : '[chunkhash].js',
  css: ({ isDev }) => isDev ? '[name].js' : '[contenthash].css',
  img: ({ isDev }) => isDev ? '[path][name].[ext]' : 'img/[hash:7].[ext]',
  font: ({ isDev }) => isDev ? '[path][name].[ext]' : 'fonts/[hash:7].[ext]',
  video: ({ isDev }) => isDev ? '[path][name].[ext]' : 'videos/[hash:7].[ext]'
}
```

아래예제는 화려한 chunk 이름을 숫자 id로 변환합니다. (`nuxt.config.js`):
```js
export default {
  build: {
    filenames: {
      chunk: ({ isDev }) => isDev ? '[name].js' : '[id].[chunkhash].js'
    }
  }
}
```

manifest 와 vendor의 사용법에 대해 자세히 알아 보시려면 [Webpack 문서](https://webpack.js.org/guides/code-splitting-libraries/)를 확인해주세요.

## hotMiddleware

- Type: `Object`

See [webpack-hot-middleware](https://github.com/glenjamin/webpack-hot-middleware) for available options.

## html.minify

- Type: `Object`
- Default:

```js
{
  collapseBooleanAttributes: true,
  collapseWhitespace: false,
  decodeEntities: true,
  minifyCSS: true,
  minifyJS: true,
  processConditionalComments: true,
  removeAttributeQuotes: false,
  removeComments: false,
  removeEmptyAttributes: true,
  removeOptionalTags: false,
  removeRedundantAttributes: true,
  removeScriptTypeAttributes: false,
  removeStyleLinkTypeAttributes: false,
  removeTagWhitespace: false,
  sortClassName: false,
  trimCustomFragments: true,
  useShortDoctype: true
}
```

Configuration for the [html-minifier](https://github.com/kangax/html-minifier) plugin used to minify
HTML files created during the build process (will be applied for *all modes*).

## loaders

> 사용자가 정의한 webpack loaders

- 타입: `Object`
- 기본값:

```js
{
  file: {},
  fontUrl: { limit: 1000 },
  imgUrl: { limit: 1000 },
  pugPlain: {},
  vue: {
    transformAssetUrls: {
      video: 'src',
      source: 'src',
      object: 'src',
      embed: 'src'
    }
  },
  css: {},
  cssModules: {
    localIdentName: '[local]_[hash:base64:5]'
  },
  less: {},
  sass: {
    indentedSyntax: true
  },
  scss: {},
  stylus: {},
  vueStyle: {}
}
```
> 주의: In addition to specifying the configurations in `nuxt.config.js`, it can also be modified by [build.extend](#extend)
> 주의: `nuxt.config.js`에 설정을 명시하는것 외에도 [build.extend](#extend)도 이를 변경할 수 있습니다.

### loaders.file

> More details are in [file-loader options](https://github.com/webpack-contrib/file-loader#options).

### loaders.fontUrl and loaders.imgUrl

> More details are in [url-loader options](https://github.com/webpack-contrib/url-loader#options).

### loaders.pugPlain

> More details are in [pug-plain-loader](https://github.com/yyx990803/pug-plain-loader) or [Pug compiler options](https://pugjs.org/api/reference.html#options).

### loaders.vue

> More details are in [vue-loader options](https://vue-loader.vuejs.org/options.html).

### loaders.css and loaders.cssModules

> More details are in [css-loader options](https://github.com/webpack-contrib/css-loader#options).
> Note: cssModules is loader options for usage of [CSS Modules](https://vue-loader.vuejs.org/guide/css-modules.html#css-modules)

### loaders.less

> You can pass any Less specific options to the `less-loader via` via `loaders.less`. See the [Less documentation](http://lesscss.org/usage/#command-line-usage-options) for all available options in dash-case.

### loaders.sass and loaders.scss

> See the [Node Sass documentation](https://github.com/sass/node-sass/blob/master/README.md#options) for all available Sass options.
> Note: `loaders.sass` is for [Sass Indented Syntax](http://sass-lang.com/documentation/file.INDENTED_SYNTAX.html)

### loaders.vueStyle

> More details are in [vue-style-loader options](https://github.com/vuejs/vue-style-loader#options).

## optimization

- Type: `Object`
- Default:

  ```js
  {
    minimize: true,
    minimizer: [
      // terser-webpack-plugin
      // optimize-css-assets-webpack-plugin
    ],
    splitChunks: {
      chunks: 'all',
      automaticNameDelimiter: '.',
      name: undefined,
      cacheGroups: {}
    }
  }
  ```

The default value of `splitChunks.name` is `true` in `dev` or `analyze` mode.

You can set `minimizer` to a customized Array of plugins or set `minimize` to `false` to disable all minimizers.
(`minimize` is being disabled for development by default)

See [Webpack Optimization](https://webpack.js.org/configuration/optimization).

## terser

- Type: `Object` or `Boolean`
- Default:

```js
{
  parallel: true,
  cache: false,
  sourceMap: false,
  extractComments: {
    filename: 'LICENSES'
  },
  terserOptions: {
    output: {
      comments: /^\**!|@preserve|@license|@cc_on/
    }
  }
}
```

Terser plugin options. Set to `false` to disable this plugin.

`soruceMap` will be enabled when webpack `confing.devtool` matches `source-?map`

See [webpack-contrib/terser-webpack-plugin](https://github.com/webpack-contrib/terser-webpack-plugin).

## optimizeCSS

- Type: `Object` or `Boolean`
- Default:
  - `false`
  - `{}` when extractCSS is enabled

OptimizeCSSAssets plugin options.

See [NMFR/optimize-css-assets-webpack-plugin](https://github.com/NMFR/optimize-css-assets-webpack-plugin).

## parallel

- Type: `Boolean`
- Default: `false`

> Enable [thread-loader](https://github.com/webpack-contrib/thread-loader#thread-loader) in webpack building

## plugins

- 타입: `Array`
- 기본값: `[]`

> Webpack plugins 추가

예제 (`nuxt.config.js`):
```js
import webpack from 'webpack'
import { version } from './package.json'
export default {
  build: {
    plugins: [
      new webpack.DefinePlugin({
        'process.VERSION': version
      })
    ]
  }
}
```

## postcss

> [PostCSS Loader](https://github.com/postcss/postcss-loader#usage) 플러그인을 사용자정의

- 타입: `Array`, `Object` (권장), `Function`, `Boolean`

**Note:** Nuxt.js 는 [PostCSS Preset Env](https://github.com/csstools/postcss-preset-env) 를 적용했습니다. 기본으로 [Stage 2 features](https://cssdb.org/) 와 [Autoprefixer](https://github.com/postcss/autoprefixer)를 사용합니다. `build.postcss.preset` 를 이용하여 설정할 수 있습니다.

- 기본값:
```js
{
  plugins: {
    'postcss-import': {},
    'postcss-url': {},
    'postcss-preset-env': {},
    'cssnano': { preset: 'default' } // disabled in dev mode
  }
}
```

예제 (`nuxt.config.js`):
```js
export default {
  build: {
    postcss: {
      plugins: {
          // `postcss-url` 사용안함
        'postcss-url': false,
        // 플러그인을 추가
        'postcss-nested': {},
        'postcss-responsive-type': {},
        'postcss-hexrgba': {}
      },
      preset: {
        autoprefixer: {
          grid: true
        }
      }
    }
  }
}
```

## profile

- Type: `Boolean`
- Default: enabled by command line argument `--profile`

> Enable the profiler in [WebpackBar](https://github.com/nuxt/webpackbar#profile)

## publicPath

> Nuxt.js는 최대한의 성능 향상을 위해 dist 파일들을 CDN에 업로드 할 수 있게 도와주는데, `publicPath`에 당신의 CDN을 간단하게 설정할 수 있습니다.

- 타입: `String`
- 기본값: `'/_nuxt/'`

예제 (`nuxt.config.js`):

```js
export default {
  build: {
    publicPath: 'https://cdn.nuxtjs.org'
  }
}
```

그러면, `nuxt build` 명령어를 실행했을 때, `.nuxt/dist/` 폴더를 당신의 CDN에 업로드 합니다.

## quiet

> Suppresses most of the build output log

- Type: `Boolean`
- Default: Enabled when a `CI` or `test` environment is detected by [std-env](https://github.com/blindmedia/std-env)

## splitChunks

- Type: `Object`
- Default:

  ```js
  {
    layouts: false,
    pages: true,
    commons: true
  }
  ```

If split codes for `layout`, `pages` and `commons` (common libs: vue|vue-loader|vue-router|vuex...).


## ssr

> Creates special webpack bundle for SSR renderer.

- Type: `Boolean`
- Default: `true` for universal mode and `false` for spa mode

This option is automatically set based on `mode` value if not provided.

## styleResources

- Type: `Object`
- Default: `{}`

This is useful when you need to inject some variables and mixins in your pages without having to import them every time.

Nuxt.js uses https://github.com/yenshih/style-resources-loader to achieve this behaviour.

You need to specify the patterns/path you want to include for the given pre-processors: `less`, `sass`, `scss` or `stylus`

:warning: You cannot use path aliases here (`~` and `@`), you need to use relative or absolute paths.

`nuxt.config.js`:

```js
{
  build: {
    styleResources: {
      scss: './assets/variables.scss',
      less: './assets/*.less',
      // sass: ...,
      // scss: ...
      options: {
        // See https://github.com/yenshih/style-resources-loader#options
        // Except `patterns` property
      }
    }
  }
}
```

## templates

> Nuxt.js allows you provide your own templates which will be rendered based on Nuxt configuration. This feature is specially useful for using with [modules](/guide/modules).

- Type: `Array`

Example (`nuxt.config.js`):

```js
export default {
  build: {
    templates: [
      {
        src: '~/modules/support/plugin.js', // `src` can be absolute or relative
        dst: 'support.js', // `dst` is relative to project `.nuxt` dir
        options: { // Options are provided to template as `options` key
          live_chat: false
        }
      }
    ]
  }
}
```

Templates are rendered using [`lodash.template`](https://lodash.com/docs/#template) you can learn more about using them [here](https://github.com/learn-co-students/javascript-lodash-templates-v-000).

## transpile

- Type: `Array<string | RegExp>`
- Default: `[]`

If you want to transpile specific dependencies with Babel, you can add them in `build.transpile`. Item in transpile can be string or regex object for matching dependencies file name.

## vueLoader

> Note: This config has been removed since Nuxt 2.0, please use [`build.loaders.vue`](#loaders)instead.

- Type: `Object`
- Default:

  ```js
  {
    productionMode: !this.options.dev,
    transformAssetUrls: {
      video: 'src',
      source: 'src',
      object: 'src',
      embed: 'src'
    }
  }
  ```

> Specify the [Vue Loader Options](https://vue-loader.vuejs.org/options.html).

## watch

> You can provide your custom files to watch and regenerate after changes. This feature is specially useful for using with [modules](/guide/modules).

- Type: `Array<String>`

```js
export default {
  build: {
    watch: [
      '~/.nuxt/support.js'
    ]
  }
}
```