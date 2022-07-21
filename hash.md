# Hash

參考 https://ruby-doc.org/core-3.1.2/Hash.html

Hash 裡是多個「key 與 value 的對應」，而且這些 key 是唯一的。

Hash 跟 Array 有一些相似的地方，但是：

* Array 的 index 永遠都是 Integer。
* Hash 的 key 可以是任何(幾乎)物件。

## Hash Data Syntax

Hash 資料的舊語法使用 “hash rocket,” =>:

```ruby
h = {:foo => 0, :bar => 1, :baz => 2}
h # => {:foo=>0, :bar=>1, :baz=>2}
```

如果 Hash 的 key 是個 Symbol，可以使用另一種新的 JSON 風格語法：

```ruby
h = {foo: 0, bar: 1, baz: 2}
h # => {:foo=>0, :bar=>1, :baz=>2}
```

Hash key 對應的值可以省略，這表示：這個值會由這個 key 所指向的變數名稱來取得：

```ruby
x = 0
y = 100
h = {x:, y:}
h # => {:x=>0, :y=>100}
```

## 常見用途

可以使用 Hash 為物件命名：

```ruby
person = {name: 'Matz', language: 'Ruby'}
person # => {:name=>"Matz", :language=>"Ruby"}
```

也可以使用 Hash 為 method 的 arguments 命名：

```ruby
def some_method(hash)
  p hash
end
some_method({foo: 0, bar: 1, baz: 2}) # => {:foo=>0, :bar=>1, :baz=>2}
```

當呼叫 method 的最後一個 argument 是個 Hash，可以省略大括號 (curly braces)：

```ruby
some_method(foo: 0, bar: 1, baz: 2) # => {:foo=>0, :bar=>1, :baz=>2}
```

可以使用 Hash 來做物件的初始化 (initialize):

```ruby
class Dev
  attr_accessor :name, :language
  def initialize(hash)
    self.name = hash[:name]
    self.language = hash[:language]
  end
end
matz = Dev.new(name: 'Matz', language: 'Ruby')
matz # => #<Dev: @name="Matz", @language="Ruby">
```

## 創建一個 Hash

你可以使用一個 [hash literal](literal.md#hash) 來建立一個 Hash object。

你也可以使用 [Hash](kernel.md#hash) method 將某些 objects 轉換成 Hashes.

你也可以呼叫 `Hash.new` method 來建立 Hash.

建一個空的 Hash:

```ruby
h = Hash.new
h # => {}
h.class # => Hash
```

你可以呼叫 `Hash.[]` method 來建立 Hash.

建一個空的 Hash:
```ruby
h = Hash[]
h # => {}
```

建一個含有初始值的 Hash:

```ruby
h = Hash[foo: 0, bar: 1, baz: 2]
h # => {:foo=>0, :bar=>1, :baz=>2}
```

你可以使用 Hash 的 literal form (大括號，curly braces) 來建立 Hash.

建一個空的 Hash:

```ruby
h = {}
h # => {}
```

建一個含有初始值的 Hash:

```ruby
h = {foo: 0, bar: 1, baz: 2}
h # => {:foo=>0, :bar=>1, :baz=>2}
```

## Hash Value 基礎

取得 Hash value 最簡單的方法 (instance method `[]`):

```ruby
h = {foo: 0, bar: 1, baz: 2}
h[:foo] # => 0
```

建立或更新 Hash value 最簡單的方法 (instance method `[]=`):

```ruby
h = {foo: 0, bar: 1, baz: 2}
h[:bat] = 3 # => 3
h # => {:foo=>0, :bar=>1, :baz=>2, :bat=>3}
h[:foo] = 4 # => 4
h # => {:foo=>4, :bar=>1, :baz=>2, :bat=>3}
```

刪除一個 Hash entry 最簡單的方法 (instance method `delete`):

```ruby
h = {foo: 0, bar: 1, baz: 2}
h.delete(:bar) # => 1
h # => {:foo=>0, :baz=>2}
```

## Entry 的順序

Hash object 按照建立的次序來呈現它的 entries. 這會出現在:

* Iterative methods 例如：`each`, `each_key`, `each_pair`, `each_value`.
* 其他與順序密切相關的 methods 例如：`shift`, `keys`, `values`.
* `inspect` method 回傳的字串

新建 Hash 的初始順序，是根據它所給定的 entries:

```ruby
h = Hash[foo: 0, bar: 1]
h # => {:foo=>0, :bar=>1}
```

新的 entries 會被加在最後面:

```ruby
h[:baz] = 2
h # => {:foo=>0, :bar=>1, :baz=>2}
```

更新某個 value 不會影響順序:

```ruby
h[:baz] = 3
h # => {:foo=>0, :bar=>1, :baz=>3}
```

但是刪除某個 entry 之後再重建，會影響順序:

```ruby
h.delete(:foo)
h[:foo] = 5
h # => {:bar=>1, :baz=>3, :foo=>5}
```

## Hash Keys

### Hash Key Equivalence

兩個 objects 會被視為相同的 hash key，如果它們的 hash value 是一致的，而且它們彼此呼叫 `eql?` method 結果都是 true.

### 變更使用中的 Hash Key

變更使用中的 Hash key 會破壞這個 hash 的 index.

下面這個 Hash 的 keys 是 Arrays:

```ruby
a0 = [ :foo, :bar ]
a1 = [ :baz, :bat ]
h = {a0 => 0, a1 => 1}
h.include?(a0) # => true
h[a0] # => 0
a0.hash # => 110002110
```

變更 array element `a0[0]` 改變了它的 hash value:

```ruby
a0[0] = :bam
a0.hash # => 1069447059
```

而且破壞了 Hash index:

```ruby
h.include?(a0) # => false
h[a0] # => nil
```

你可以使用 `rehash` method 來修復 hash index:

```ruby
h.rehash # => {[:bam, :bar]=>0, [:baz, :bat]=>1}
h.include?(a0) # => true
h[a0] # => 0
```

使用字串做為 key 則是安全的。這是因為一個未被凍結的字串 (unfrozen String) 被當做 key 傳遞的時候，會被取代為另一個重製且凍結的字串:

```ruby
s = 'foo'
s.frozen? # => false
h = {s => 0}
first_key = h.keys.first
first_key.frozen? # => true
```

### 使用者定義 Hash Keys

Objects 必須實做 `hash` 以及 `eql?` 這兩個 method，才能被用作 Hash key.
註: 如果 Hash 使用 `compare_by_identity`，這個條件就不須要，因為 key 的比較是看它的 object id，而不是 `hash` 和 `eql?`.
