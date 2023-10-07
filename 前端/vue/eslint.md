## 1. 对于compoents中的name

> 使用驼峰法或者横线命名，并且需要两个以上

## 2. 对于template中

> 导入vueTemmplate就要用，三步法，导入，注册，使用

## 3. 对于data()中定义的数据

```
'title:' is defined but never used.eslint
```



> 在 [package](https://so.csdn.net/so/search?q=package&spm=1001.2101.3001.7020).json中，找到 eslintConfig ，在 rules 里添加 "no-unused-vars": "off" 





