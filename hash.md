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

Object 定義 `hash` 和 `eq?` 的基本實做，使用每個 object 能成為獨特的 key.
通常，用戶定義的 classes 會想要覆寫這些 methods 來提供有意義的行為。

hash method 的典型實做是依照這個 object 的資料，而 `eql?` 通常會是被覆寫的 `==` method 的別名：

```ruby
class Book
  attr_reader :author, :title

  def initialize(author, title)
    @author = author
    @title = title
  end

  def ==(other)
    self.class === other &&
      other.author == @author &&
      other.title == @title
  end

  alias eql? ==

  def hash
    @author.hash ^ @title.hash # XOR
  end
end

book1 = Book.new 'matz', 'Ruby in a Nutshell'
book2 = Book.new 'matz', 'Ruby in a Nutshell'

reviews = {}

reviews[book1] = 'Great reference!'
reviews[book2] = 'Nice and compact!'

reviews.length #=> 1
```

## Default Values

`[]`, `values_at` 以及 `dig` 這些 method 需要回傳某個 key 所對應的 value.
當這個 key 找不到的時候，這個 value 會由這個 hash 的 default proc (如果有的話)，或由它的預設值 (初始設定是 `nil`) 來決定。

你可以使用 default 這個 method 來取得這個預設值：

```ruby
h = Hash.new
h.default # => nil
```

你可以在呼叫 `Hash.new` 時傳遞一個 argument 來設定預設值，或是使用 `defalut=` 這個 method.

```ruby
h = Hash.new(-1)
h.default # => -1
h.default = 0
h.default # => 0
```

找不到某個 key 的時候，`[]` 、`values_at ` 以及 `dig` 就會回傳這個預設值：

```ruby
counts = {foo: 42}
counts.default # => nil (default)
counts[:foo] = 42
counts[:bar] # => nil
counts.default = 0
counts[:bar] # => 0
counts.values_at(:foo, :bar, :baz) # => [42, 0, 0]
counts.dig(:bar) # => 0
```

要注意的是，這個預設值在被使用的時候不會先做複製 (duplicate)。
所以，不建議將預設值設成一個內容可變的 object (mutable object):

```ruby
synonyms = Hash.new([])
synonyms[:hello] # => []
synonyms[:hello] << :hi # => [:hi], 預設值被改變了！
synonyms.default # => [:hi]
synonyms[:world] << :universe
synonyms[:world] # => [:hi, :universe], 唉呀！
synonyms.keys # => [], 唉呀！Hash 還是空的！
```

如果要將預設值設為 mutable object，建議使用 default proc，如下說明。

### Default Proc

如果 Hash 設定了 default proc (預設程序)，method `[]` 回傳的預設值就完全由 default proc 決定。

你可以使用 `default_proc` 這個 method 來取得某個 hash 的 default proc:

```ruby
h = Hash.new
h.default_proc # => nil
```

如果要設定 default proc，你可以在呼叫 `Hash.new` 的時候傳遞一個 block，或是呼叫 `default_proc=` 這個 method:

```ruby
h = Hash.new { |hash, key| "Default value for #{key}" }
h.default_proc.class # => Proc
h.default_proc = proc { |hash, key| "Default value for #{key.inspect}" }
h.default_proc.class # => Proc
```

在設定了 default proc 的情況下 (也就是說，它不是 nil)，如果使用不存在的 key 呼叫 `[]` method，`[]` method 會呼叫 default proc，並傳入兩個參數：1. 這個 Hash object 本身，2. 這個不存在的 key，然後回傳這 proc 的傳回值：

```ruby
h = Hash.new { |hash, key| "Default value for #{key}" }
h[:nosuch] # => "Default value for nosuch"
```

注意在上例中，key 值是 :nosuch 的 entry 並沒有被建立：

```ruby
h.include?(:nosuch) # => false
```

可以在 proc 裡面新建 entry:

```ruby
synonyms = Hash.new { |hash, key| hash[key] = [] }
synonyms.include?(:hello) # => false
synonyms[:hello] << :hi # => [:hi]
synonyms[:world] << :universe # => [:universe]
synonyms.keys # => [:hello, :world]
```

注意：設定 default proc 會清除 default value，反之亦然。

## What's Here

### 用來創建 Hash 的 Methods

| method | 說明 |
| ------ | ---- |
| `::[]`  | 根據給定的 objects 創建新的 hash 並回傳 |
| `::new` | 回傳一個新的空 hash. |
| `::try_convert` | 回傳根據給定物件創建的新 hash. |

### 用來設定 Hash 狀態的 Methods

| method | 說明 |
| ------ | ---- |
| `compare_by_identity` | 設定為：比較 keys 的時候只考慮 identity. |
| `default=`      | 設定預設值 |
| `default_proc=` | 設定 default proc |
| `rehash`        | 通過重新計算每個 key 的 hash index 來重建 hash table. |

### 用來查詢的 Methods

| method | 說明 |
| ------ | ---- |
| `any?` | 回傳是否有任何元素滿足給定條件。 |
| `compare_by_identity?` | 回傳比較 keys 的時候，hash 是否只考慮 identity. |
| `default` | 回傳預設值，或給定 key 的預設值。 |
| `default_proc` | 回傳 default proc. |
| `empty?` | 回傳是否沒有 entries. |
| `eql?`   | 回傳給定的 object 是否等於 self. |
| `hash`   | 回傳整數 hash code |
| `has_value?` | 回傳某個給定的 object 是否存在於 self 的 values. |
| `include?`, `has_key?`, `member?`, `key?` | 回傳某個物件是否是 self 的 keys 之一 |
| `length`, `size` | 回傳 entries 數量。 |
| `value?` | 回傳某個給定的 object 是否存在於 self 的 values. |

### 用來比較的 Methods

| method | 說明 |
| ------ | ---- |
| `#<`  | 回傳：self 是否是給定 object 的真子集(proper subset). |
| `#<=` | 回傳：self 是否是給定 object 的子集合(subset). |
| `#==` | 回傳：給定 object 是否等於 self. |
| `#>`  | 回傳：self 是否是給定 object 的 proper superset. |
| `#>=` | 回傳：self 是否是給定 object 的 superset. |

### Methods for Fetching

| method | 說明 |
| ------ | ---- |
| `[]`    | 給定某個 key, 回傳對應的 value |
| `assoc` | 給定某個 key, 回傳一個包含兩個 element 的 array，第一個 element 是 key，第二個 element 是它的 value. |
| `dig`   | 給定某個 key 以及更多的 arguments，回傳巢狀物件裡的物件。 |
| `fetch` | 給定某個 key, 回傳對應的 value |
| `fetch_values` | 給定多個 key, 回傳 array 包含對應的 values. |
| `key`  | 給定某個 value, 回傳第一個符合的 entry 的 key |
| `keys` | 回傳一個 array 包含全部的 keys. |
| `rassoc` | 給定某個 value, 找到第一筆符合的 entry, 回傳一個 array，包含 key 跟 value 兩個 elements. |
| `values` | 回傳一個 array 包含全部的 values. |
| `values_at` | 給定多個 key, 回傳 array 包含對應的 values. |

### 用於 Assigning 的 Methods

| method | 說明 |
| ------ | ---- |
| `[]=`, `store` | 將「給定的 key」與「給定的值」建立關聯。 |
| `merge` | 將 self 複製為一份新 hash，然後將給定的每一個 hash 合併到這個新 hash，然後回傳。 |
| `merge!`, `update` | 將每個給定的 hash 合併到 self. |
| `replace` | 將 self 的全部內容替換為給定的 hash 的內容。 |

### 用於刪除的 Methods

這些 methods 從 self 移除 entries:

| method | 說明 |
| ------ | ---- |
| `clear` | 從 self 中刪除所有 entries. |
| `compact!` | 從 self 中刪除所有值為 nil 的 entries. |
| `delete` | 刪除給定 key 的 entry. |
| `delete_if` | 刪除給定 block 選擇的 entries. |
| `filter!`, `select!` | 僅保留給定 block 選擇的那些 entries. |
| `keep_if` | 僅保留給定 block 選擇的那些 entries. |
| `reject!` | 刪除給定 block 選擇的 entries. |
| `shift` | 移除並回傳第一個 entry. |

這些 methods 回傳 self 的副本，其中刪除了一些條目：

| method | 說明 |
| ------ | ---- |
| `compact` | 回傳刪除所有 nil 值條目的 self 副本。 |
| `except`  | 回傳 self 的副本，其中刪除了指定 keys 的 entries. |
| `filter`, `select` | 回傳 self 的副本，其中僅包含給定 block 選擇的那些 entries. |
| `reject` | 回傳 self 的副本，其中刪除了給定 blok 指定的 entries. |
| `slice` | 回傳一個 hash，包含給定 keys 的 entries. |

### 用於 Iterating 的 Methods

| method | 說明 |
| ------ | ---- |
| `each`, `each_pair` | 使用每一對 key-value pair 呼叫給定的 block. |
| `each_key` | 使用每一個 key 呼叫給定的 block. |
| `each_value` | 使用每一個 value 呼叫給定的 block. |

### 用於轉換的 Methods

| method | 說明 |
| ------ | ---- |
| `inspect`, `to_s` | 回傳一個包含全部 hash entries 的新字串。 |
| `to_a` | 回傳一個由 2-element array 組成的新 array；每個 2-element array 都包含一對來自 self 的 key-value. |
| `to_h` | 如果 selft 是 Hash，就回傳 self；如果 self 是 Hash 的 subclass，就回傳包含來自 self 的 entries Hash. |
| `to_proc` | 回傳一個 proc, 這個 proc 接受一個 key 參數，會將給定的 key 對應到它的 value. |
