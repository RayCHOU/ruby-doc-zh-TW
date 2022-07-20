# Array

https://ruby-doc.org/core-3.1.2/Array.html

Array 是一個有順序的、integer-indexed 的物件的集合，array 裡的物件稱為 elements.  
任何 object 都可以是 array 的 element.  

## Array Indexes

Array 的 index 從 0 開始，跟 C、Java 一樣。

正數的 index 是從第一個 element 開始的 offset:
* index 0 表示第一個元素
* index 1 表示第二個元素
* …依此類推

負數的 index 是從 array 最後面算過來的 offset:
* index -1 表示最後一個元素
* index -2 表示倒數第二個元素
* …依此類推

## Creating Arrays

可以使用 [array literal](https://ruby-doc.org/core-3.1.2/doc/syntax/literals_rdoc.html#label-Array+Literals) 來建立 array 物件
```ruby
[1, 2, 3]
```

字串組成的 array 可以使用 [String-Array Literals](https://ruby-doc.org/core-3.1.2/doc/syntax/literals_rdoc.html#label-25w+and+-25W-3A+String-Array+Literals) 簡化
```ruby
%w[foo bar baz]       # => ["foo", "bar", "baz"]
```

Array 可以包含不同類型的物件。  
例如下面這個 array 包含了一個 Integer、一個 String、一個 Float:
```rubuy
ary = [1, "two", 3.0] #=> [1, "two", 3.0]
```

也可以呼叫 Array.new 來建立 array,  
呼叫時可以不傳參數，也可以只傳一個參數 (Array 的初始大小)，  
或是傳兩個參數 (初始大小 以及 預設物件).
```ruby
ary = Array.new    #=> []
Array.new(3)       #=> [nil, nil, nil]
Array.new(3, true) #=> [true, true, true]
```

注意：第二個參數所建立的 array 會指向同一個物件。  
因此，建議只用在 array 的元素是「本來就是不可變的物件」(natively immutable objects)，  
例如：Symbols、數字、true 或 false.

如果要建立一個 array 裡面的元素是不同的 objects，可以傳遞一個 block。  
這是個安全的方法，元素可以是「可變的物件」(mutable objects)，例如：hashes, 字串 或其他 arrays:
```ruby
Array.new(4) {Hash.new}    #=> [{}, {}, {}, {}]
Array.new(4) {|i| i.to_s } #=> ["0", "1", "2", "3"]
```

這也是快速建立 多維陣列 (multi-dimensional arrays) 的方法：
```ruby
empty_table = Array.new(3) {Array.new(3)}
#=> [[nil, nil, nil], [nil, nil, nil], [nil, nil, nil]]
```

也可以使用 Kernel 提供的 Array() method 來建立 array，  
它會試著呼叫參數的 to_ary 方法，然後試 to_a 方法。
```ruby
Array({:a => "a", :b => "b"}) #=> [[:a, "a"], [:b, "b"]]
```

