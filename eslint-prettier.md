# 语法校验与自动格式化配置

- vue-cli 初始化项目后安装插件

  - vscode 插件安装 prettier code formmater
  - npm install eslint-config-prettier、eslint-plugin-prettier

- package.json 配置文件修改

  - eslintConfig 下配置 extends
    - `['standard', 'prettier', 'plugin:vue/recommended']`

- prettier 配置

  - 项目根目录添加 .prettierrc 文件
  - 添加自定义格式化配置
    - 'endOfLine': 'auto'
    - 'semi': true
    - ...

- vscode 配置

  - 打开 setting.json, 删除历史配置
  - 添加 'editor.formatOnSave': true
  - 对应不同文件类型定义格式化插件(都用 prettier 即可)

```Json
      "[javascript]": {
            "editor.defaultFormatter": "esbenp.prettier-vscode"
        }
```

- vue 文件格式化配置

  - vue 文件使用 vetur 格式化, vscode 下载 vetur 插件
  - setting.json 添加配置

  ```Json
    // vue 文件中的 js、html 使用 prettier 格式化
    "vetur.format.defaultFormatter.js": "prettier-eslint",
    "vetur.format.defaultFormatter.html": "prettier",
    "[vue]": {
        "editor.defaultFormatter": "octref.vetur"
    }

  ```
