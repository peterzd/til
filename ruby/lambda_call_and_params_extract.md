# lambda传参数

```ruby
def foo(a)
  puts a
  lambda do |b|
    puts b
  end
end

foo(1).call(2)
# 
1
2
```
这里`foo`方法要传一个值进去，同时，`call`方法也需要一个参数，这个值是传给lambda里的参数b使用的。另外，lambda的参数不能捕获foo方法里的参数

# Rails Hash#extract!
```ruby
hash = { a: 1, b: 2, c: 3 }
hash.extract! :a # => { a: 1 } 同时，hash = { b: 2, c: 3 }
hash.extract! :d # => {}
```
