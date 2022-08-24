# String

## String Literal

### Escape sequence
常用

| sequence | 說明 |
| -- | ----------------------------------- |
| \n | newline (line feed), ASCII 0Ah (LF) |
| \r | carriage return, ASCII 0Dh (CR)     |
| \t | horizontal tab, ASCII 09h (TAB).    |
| \unnnn | Unicode 字元, nnnn 是 16進位的四個數字 ([0-9a-fA-F]) |
| \u{nnnn ...} | Unicode 字元(可以多個), 每個 nnnn 是16進位 1至6個數字 ([0-9a-fA-F]) |


[完整列表](https://ruby-doc.org/core-3.1.2/doc/syntax/literals_rdoc.html#label-String+Literals)

### Inpterpolation

雙引號字串允許使用 `#{...}` 插入其他值:
```ruby
"One plus one is two: #{1 + 1}"
```

* `#{ @foo }` 可以簡寫為 `#@foo`
* `#{ @@foo }` 可以簡寫為 `#@@foo`
* `#{ $foo }` 可以簡寫為 `#$foo`

### Percent Literals

適用情境：單行字串、字串中有雙引號、需要 interpolation

```ruby
%(<tr><td class="name">#{name}</td>)
```

### Here Document Literals

參考 

* Ruby-Doc => Literals => [Here Document Literals](https://ruby-doc.org/core-3.1.2/doc/syntax/literals_rdoc.html#label-Here+Document+Literals)
* The Ruby Style Guide => [Heredocs](https://rubystyle.guide/#heredocs)

```ruby
xml = <<~XML
  def foo
    bar
  end
XML
```

## Substitution Methods
這些 methods 用來做取代:

| method | 說明 | return |
| ------ | ---- | ----- |
| sub | 取代一次 (或沒有) | 新字串 |
| sub! | 取代一次 (或沒有) | self |
| gsub | 零或多次取代 | 新字串 |
| gsub! | 零或多次取代 | self |

## []

https://ruby-doc.org/core-3.1.2/String.html#method-i-5B-5D

## +

https://ruby-doc.org/core-3.1.2/String.html#method-i-2B 

處理大量資料時，避免使用 +，改用 <<，因為 String#+ 會建立一堆新字串物件。
參考 The Ruby Style Guide => Strings => [String Concatenation](https://rubystyle.guide/#string-interpolation)

## <<

https://ruby-doc.org/core-3.1.2/String.html#method-i-3C-3C 

## *

https://ruby-doc.org/core-3.1.2/String.html#method-i-2A

## =~

```ruby
'foo' =~ /f/ # => 0
'foo' =~ /o/ # => 1
'foo' =~ /x/ # => nil
```

## gsub

https://ruby-doc.org/core-3.1.2/String.html#method-i-gsub 

## gsub!

https://ruby-doc.org/core-3.1.2/String.html#method-i-gsub-21 

gsub!(pattern, replacement) → self 或 nil  
gsub!(pattern) {|match| ... } → self or nil  
gsub!(pattern) → an_enumerator

## match?

```ruby
'foo'.match?(/o/) # => true
'foo'.match?('o') # => true
'foo'.match?(/x/) # => false
```
