# work.template.vscode

## vscode 使用过程中的相关配置信息

```js
{
"editor.tabSize": 2,
"eslint.validate": [
"javascript",
"javascriptreact",
{
"language": "vue",
"autoFix": true
}
],
"eslint.autoFixOnSave": true,
"eslint.run": "onSave",
"javascript.format.enable": false,
"prettier.printWidth": 100,
"prettier.singleQuote": true,
"prettier.trailingComma": "es5",
"prettier.eslintIntegration": true,
"vueStyle.formatOnSave": true,
"editor.formatOnSave": true,
"files.autoSave": "onWindowChange",
"emmet.syntaxProfiles": {
"vue-html": "html",
"vue": "html"
},
"workbench.iconTheme": "vscode-icons",
"terminal.integrated.shell.windows": "C:\\Program Files\\Git\\bin\\bash.exe",
"svn.path": "C:\\Program Files\\SlikSvn\\bin\\svn.exe",
"less.compile": {
"compress": true, // true => remove surplus whitespace
"sourceMap": false, // false => Do'nt generate source maps (.css.map files)
"out": true, // true => output .css files (overridable per-file, see below)
}
}
```