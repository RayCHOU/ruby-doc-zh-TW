# File

Ruby-Doc => [File](https://ruby-doc.org/core-3.1.2/File.html), [IO](https://ruby-doc.org/core-3.1.2/IO.html)

## read

Ruby-Doc => IO => [::read](https://ruby-doc.org/core-3.1.2/IO.html#method-c-read)

讀入整個檔案：
```ruby
File.read("testfile") 
```

## foreach

處理檔案裡的每一行。

參考 Ruby-Doc => IO => [::foreach](https://ruby-doc.org/core-3.0.0/IO.html#method-c-foreach)

```ruby
File.foreach("testfile") {|x| print "GOT ", x }
```

## open

開啟檔案。

參考 Ruby-Doc => File =>  [::open](https://ruby-doc.org/core-3.1.2/File.html#method-c-open)

```ruby
fo = File.open('T01.txt', 'w')
fo.puts 'test'
```

## puts

https://ruby-doc.org/core-3.1.2/IO.html#method-i-puts 

puts(obj, ...) → nil

把傳進來的一個或多個物件寫出去。  
如果物件結尾沒有換行，會自動加上換行。

如果傳進來一個 array，會寫出每個 element 並加上換行。

如果傳進來的不是字串、也不是 array，會呼叫該物件的 to_s 方法。

如果沒有傳進任何參數，會寫出一個換行。

