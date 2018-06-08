# chapter 2

``` Python
print 'Hello World!'
```

下划线(\_)在解释器中有特别的含义，表示最后一个表达式的值。

``` Python
print "%s is number %d!" % ("Python", 1)
```

``` Python
logfile = open('/tmp/mylog.txt', 'a')
print >> logfile, 'Fatal error: invalid input!'
logfile.close()
```
