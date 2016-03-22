# ObjectSpace

> The ObjectSpace module contains a number of routines that interact with the garbage collection facility and allow you to traverse all living objects with an iterator.
> ObjectSpace这个module提供一系列的方法，可以跟GC进行交互，并提供一个iterator允许我们在存在的objects里穿行

ruby提供一个`ancestors`方法，可以得到一个类的所有祖先类，但没有直接提供一个`descendants`来得到一个类的所有子类，可以这样进行扩展：

```ruby
class Parent
  def self.descendants
    ObjectSpace.each_object(Class).select { |klass| klass < self }
  end
end

class Child < Parent
end

class GrandChild < Child
end

puts Parent.descendants # => GrandChild, Child
puts Child.descendants  # => GrandChild
```
