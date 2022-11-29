# Springboot项目的部署

## 暂时部署

```linux
java -jar xxx.jar
```

## 永久部署

```linux
nohup java -jar xxx.jar &> xxx.log &
```

## 停止

1. 查看运行项目及端口号

   ```linux
   ps -ef | grep xxx.jar
   ```

2. 关闭端口对应的服务

   ```linux
   kill -9 端口号
   ```

3. 删除项目jar包

   ```linux
   rm -f path/xxx.jar
   ```

   ​