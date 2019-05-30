# lint-staged-简易实现

标准配置
```
"husky": {
  "hooks": {
    "pre-commit": "lint-staged"
  }
},
"lint-staged": {
  "*.js": [
    "npx eslint --fix",
    "git add"
  ],
  "*.vue": [
    "npx eslint --fix",
    "git add"
  ]
},
```


或者自己写



```js
console.log('start check code')
const shell = require('shelljs')

// 查找暂存区的文件
let files = shell.exec('git diff --cached --name-only')

let filesArr = files.trim().split('\n')
// 筛选需要格式化的文件
let filesNeedFix = filesArr.filter(e => {
  return getFileType(e) === 'js' || getFileType(e) === 'vue' || getFileType(e) === 'jsx'
})
// console.log(filesNeedFix)
if (filesNeedFix.length) {
  if (shell.exec(`npx eslint --fix  ${filesNeedFix.join(' ')}`).code !== 0) {
    console.log('有错误，请修改')
    // 退出commit
    process.exit(1)
  } else {
    shell.exec(`git add ${filesNeedFix.join(' ')}`)
  }
  console.log('end check')
}

/**
 * 获取文件后缀
 * @param {String} filePath
 */
function getFileType(filePath) {
  var startIndex = filePath.lastIndexOf('.')
  if (startIndex != -1) return filePath.substring(startIndex + 1, filePath.length).toLowerCase()
  else return ''
}
```
