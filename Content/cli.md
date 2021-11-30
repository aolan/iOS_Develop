## 搭建typescript项目 + 开发脚手架cli工具

参考：https://www.jianshu.com/p/67e3bf071c56

### 1、创建项目

```shell

# 创建项目，并安装typescript
mkdir ts-project && cd ts-project && npm init -y && npm i typescript -D

# 创建tsconfig.json文件
npx tsc --init

# 创建目录和代码文件
mkdir src && touch src/index.ts

```


### 2、修改 tsconfig.json 文件

```json
{
  "compileOnSave": true,
  "compilerOptions": {
    "target": "ES2018",
    "module": "commonjs",
    "moduleResolution": "node",
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true,
    "inlineSourceMap":true,
    "noImplicitThis": true,
    "noUnusedLocals": true,
    "stripInternal": true,
    "pretty": true,
    "declaration": true,
    "outDir": "lib",
    "baseUrl": "./",
    "paths": {
      "*": ["src/*"]
    }
  },
  "exclude": [
    "lib",
    "node_modules"
  ]
}

```
