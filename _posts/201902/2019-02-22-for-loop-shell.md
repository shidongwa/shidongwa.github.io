
循环中可以执行与时序相关的shell命令。

```shell
max=10
for i in `seq 1 25`
do
    echo "$i"
done
```