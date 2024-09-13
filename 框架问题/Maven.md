
#### 1 创建模板项目的时候很慢

解决方法：在 IDEA 的 Settings 窗口的 Build, Execution, Deployment > Build Tools > Maven > Runner 中对 VM Option 设置为以下参数： 

``` bash
-DarchetypeCatalog=internal
```


#### 2  Maven 启动项目quickStart没有resource文件夹

这个模板就没有resource文件夹
```bash
maven-archetype-quickstart
```

*如果没有的话可以自己 new Directory 可以生成的*



