# Method

## 方法名稱

method 命名方式 參考 The Ruby Style Guide => Naming Conventions => [Snake Case for Symbols, Methods and Variables](https://rubystyle.guide/#snake-case-symbols-methods-vars)

使用 snake_case，例如：
```ruby
def hello_world
  ...
end
```

## 內縮

內縮不是必要的，但是比較容易閱讀。  
內縮要用 TAB 或空格？空格要用幾格？  
參考 The Ruby Style Guide => Source Code Layout => [Indentation](https://rubystyle.guide/#spaces-indentation)  
每內縮一層使用兩個半形空格 (也叫做 soft tabs)。

例如
```ruby
def hello
  puts "Hello World!"
end
```

## 參數

呼叫 method 時，小括號可以省略：
```ruby
def hello(name)
  puts "Hello #{name}!"
end

hello("Matz")
hello "Matz" # 省略小括號也可以
```

### Positional argument

例如：
```ruby
def hello(first_name, last_name)
  puts "Hello #{first_name} #{last_name}!"
end

hello('Heaven', 'Chou')
hello('Chou', 'Heaven')
```

### Keyword argument

keyword argument 是有別於 positional arguments 的另一種參數傳遞方法。

例如以下程式使用 keyword argument:
```ruby
def hello(first_name: 'Ray', last_name: 'Chou')
  puts "Hello #{first_name} #{last_name}!"
end

hello
hello(first_name: 'Heaven', last_name: 'Chou')
hello(last_name: 'Chou', first_name: 'Heaven')
```

