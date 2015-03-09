有的时候需要某些Action允许跨域访问，可以通过设置`cors`方式来实现，代码如下：

```ruby
headers['Access-Control-Allow-Origin'] = '*'
headers['Access-Control-Allow-Methods'] = 'POST, GET, OPTIONS'
headers['Access-Control-Request-Method'] = '*'
headers['Access-Control-Allow-Headers'] = '*'
```

可以把这段代码封成一个方法，在需要它的Action里使用
