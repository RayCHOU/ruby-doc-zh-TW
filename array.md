# 陣列 (Array, 或稱 矩陣)

https://ruby-doc.org/core-3.1.2/Array.html

陣列 是一個有順序的、integer-indexed 的物件(object)的集合，陣列裡的物件稱為元素(elements).  
任何物件都可以是陣列的元素。

## Array Indexes

Array 的 index 從 0 開始，跟 C、Java 一樣。

正數的 index 是從第一個元素開始的 offset:
* index 0 表示第一個元素
* index 1 表示第二個元素
* …依此類推

負數的 index 是從陣列最後面算過來的 offset:
* index -1 表示最後一個元素
* index -2 表示倒數第二個元素
* …依此類推

## Creating Arrays

可以使用 [array literal](https://ruby-doc.org/core-3.1.2/doc/syntax/literals_rdoc.html#label-Array+Literals) 來建立 陣列物件

```ruby
[1, 2, 3]
```

字串組成的陣列可以使用 [String-Array Literals](https://ruby-doc.org/core-3.1.2/doc/syntax/literals_rdoc.html#label-25w+and+-25W-3A+String-Array+Literals) 簡化

```ruby
%w[foo bar baz]       # => ["foo", "bar", "baz"]
```

可以使用 [Array](https://ruby-doc.org/core-3.1.2/Kernel.html#method-i-Array) method 將某些物件轉換成為 Array.

Array 可以包含不同類型的物件。  
例如下面這個陣列包含了一個 Integer、一個 String、一個 Float:

```ruby
ary = [1, "two", 3.0] #=> [1, "two", 3.0]
```

也可以呼叫 Array.new 來建立 陣列,  
呼叫時可以不傳參數，也可以只傳一個參數 (Array 的初始大小)，  
或是傳兩個參數 (初始大小 以及 預設物件).

```ruby
ary = Array.new    #=> []
Array.new(3)       #=> [nil, nil, nil]
Array.new(3, true) #=> [true, true, true]
```

注意：第二個參數所建立的 陣列 會指向同一個物件。  
因此，建議只用在 陣列 的元素是「本來就是不可變的物件」(natively immutable objects)，  
例如：Symbols、數字、true 或 false.

如果要建立一個 陣列 裡面的元素是不同的 objects，可以傳遞一個 block。  
這是個安全的方法，元素可以是「可變的物件」(mutable objects)，例如：hashes, 字串 或其他陣列：

```ruby
Array.new(4) {Hash.new}    #=> [{}, {}, {}, {}]
Array.new(4) {|i| i.to_s } #=> ["0", "1", "2", "3"]
```

這也是快速建立 多維陣列 (multi-dimensional arrays) 的方法：

```ruby
empty_table = Array.new(3) {Array.new(3)}
#=> [[nil, nil, nil], [nil, nil, nil], [nil, nil, nil]]
```

也可以使用 Kernel 提供的 `Array()` method 來建立陣列，  
它會試著呼叫參數的 `to_ary` 方法，然後試 `to_a` 方法。

```ruby
Array({:a => "a", :b => "b"}) #=> [[:a, "a"], [:b, "b"]]
```

## 使用範例

除了通過 `Enumerable` module 混合的方法之外，`Array` class 還具有用於 存取、搜尋和以其他方式操作 arrays 的專有方法。

一些比較常見的舉例說明如下。

## 存取 Array 的 Elements

可以使用 `Array#[]` 方法取得陣列裡的元素。它可以採用單個整數參數（數字索引）、一對參數（開始和長度）或範圍。負數的索引表示從後面倒數，-1 是最後一個元素。

```ruby
arr = [1, 2, 3, 4, 5, 6]
arr[2]    #=> 3
arr[100]  #=> nil
arr[-3]   #=> 4
arr[2, 3] #=> [3, 4, 5]
arr[1..4] #=> [2, 3, 4, 5]
arr[1..-3] #=> [2, 3, 4]
```

另一個存取某個特定陣列元素的方式是使用 `at` 方法：

```ruby
arr.at(0) #=> 1
```

`slice` method 運作的方式跟 `Array#[]` 一樣。

如果索引值超出陣列的邊界，會發生一個錯誤；或者，可以呼叫 fetch 並提供一個預設值：

```ruby
arr = ['a', 'b', 'c', 'd', 'e', 'f']
arr.fetch(100) #=> IndexError: index 100 outside of array bounds: -6...6
arr.fetch(100, "oops") #=> "oops"
```

`first` 跟 `last` 這兩個特別的方法會分別回傳陣列的第一個、最後一個元素。

```ruby
arr.first #=> 1
arr.last  #=> 6
```

使用 `take` 回傳陣列的前面 n 個元素:

```ruby
arr.take(3) #=> [1, 2, 3]
```

`drop` 與 `take` 相反，它回傳 n 個元素後面的元素：

```ruby
arr.drop(3) #=> [4, 5, 6]
```

## 取得關於某個 `Array` 的資訊

Arrays 會一直記住自己的長度。要查詢某個陣列包含的元素數量，請使用 `length`、`count` 或 `size`。

```ruby
browsers = ['Chrome', 'Firefox', 'Safari', 'Opera', 'IE']
browsers.length #=> 5
browsers.count #=> 5
```

查看某個陣列是否包含任何元素：

```ruby
browsers.empty? #=> false
```

查看某個特定的 item 是否存在陣列裡面：

```ruby
browsers.include?('Konqueror') #=> false
```

## 新增 Items 到 Array 裡

使用 `push` 或 `<<` 可以將 items 加到陣列的最後面：

```ruby
arr = [1, 2, 3, 4]
arr.push(5) #=> [1, 2, 3, 4, 5]
arr << 6    #=> [1, 2, 3, 4, 5, 6]
```

`unshift` 會將某個新的 item 加到陣列的最前面：

```ruby
arr.unshift(0) #=> [0, 1, 2, 3, 4, 5, 6]
```

使用 `insert` 可以將某個新的元素插入某個陣列的任何位置：

```ruby
arr.insert(3, 'apple')  #=> [0, 1, 2, 'apple', 3, 4, 5, 6]
```

使用 `insert` method 也可以一次插入多個值：

```ruby
arr.insert(3, 'orange', 'pear', 'grapefruit')
#=> [0, 1, 2, "orange", "pear", "grapefruit", "apple", 3, 4, 5, 6]
```

## 移除 Array 裡的 Items

`pop` 會移除並回傳陣列的最後一個元素：

```ruby
arr =  [1, 2, 3, 4, 5, 6]
arr.pop #=> 6
arr #=> [1, 2, 3, 4, 5]
```

`shift` 則是移除並回傳陣列的第一個元素：

```ruby
arr.shift #=> 1
arr #=> [2, 3, 4, 5]
```

刪除某個特定位置的元素：

```ruby
arr.delete_at(2) #=> 4
arr #=> [2, 3, 5]
```

如果要刪除某個特定的元素，不管它出現在陣列的任何位置，可以使用 `delete`:

```ruby
arr = [1, 2, 2, 3]
arr.delete(2) #=> 2
arr #=> [1,3]
```

如果你需要將陣列裡所有的 nil 值都去除，`compact` 是個有用的方法：

```ruby
arr = ['foo', 0, nil, 'bar', 7, 'baz', nil]
arr.compact  #=> ['foo', 0, 'bar', 7, 'baz']
arr          #=> ['foo', 0, nil, 'bar', 7, 'baz', nil]
arr.compact! #=> ['foo', 0, 'bar', 7, 'baz']
arr          #=> ['foo', 0, 'bar', 7, 'baz']
```

另一個常見的需求是要將陣列裡重複的元素去除。  
可以使用 non-destructive (非破壞性的) `uniq`，或是 destructive (破壞性的) method `uniq!`

```ruby
arr = [2, 5, 6, 556, 6, 6, 8, 9, 0, 123, 556]
arr.uniq #=> [2, 5, 6, 556, 8, 9, 0, 123]
```

## Iterating over (遍歷) Arrays

就跟其他 include 了 `Enumerable` 這個 module 的其他 classes 一樣，`Array` 也有 `each` 這個方法，它定義了哪些元素要被 iterated、如何被 iterated.  
以 Array 的 `each` 來說，Array 裡的所有元素都會依序被傳送給提供的 block.

注意下面的操作不會改變陣列:

```ruby
arr = [1, 2, 3, 4, 5]
arr.each {|a| print a -= 10, " "}
# prints: -9 -8 -7 -6 -5
#=> [1, 2, 3, 4, 5]
```

另一個有時候有用的 iterator 是 `reverse_each`，它會反向 iterate 陣列裡的元素：

```ruby
words = %w[first second third fourth fifth sixth]
str = ""
words.reverse_each {|word| str += "#{word} "}
p str #=> "sixth fifth fourth third second first "
```

map 方法可以用來根據某個陣列建立新的陣列，根據提供的 block 變更陣列裡的值：

```ruby
arr.map {|a| 2*a}     #=> [2, 4, 6, 8, 10]
arr                   #=> [1, 2, 3, 4, 5]
arr.map! {|a| a**2}   #=> [1, 4, 9, 16, 25]
arr                   #=> [1, 4, 9, 16, 25]
```

## 從 Array 選取某些 Items

可以根據 block 裡面定義的規則從陣列中選擇元素。  
這個選擇的方式可以是破壞性的(destructive)，也可以不是破壞性的(non-destructive)。  
破壞性操作會修改原來的陣列，而非破壞性方法通常會返回一個包含所選元素的新陣列，但保持原始陣列不變。

### 非破壞性選取

```ruby
arr = [1, 2, 3, 4, 5, 6]
arr.select {|a| a > 3}       #=> [4, 5, 6]
arr.reject {|a| a < 3}       #=> [3, 4, 5, 6]
arr.drop_while {|a| a < 4}   #=> [4, 5, 6]
arr                          #=> [1, 2, 3, 4, 5, 6]
```

### 破壞性選取

`select!` 和 `reject!` 是相對於 `select` 和 `reject` 的破壞性方法。

類似於 `select` vs. `reject`，當提供相同的 block 的時候，`delete_if` 跟 `keep_if` 也是會回傳相反的結果：

```ruby
arr.delete_if {|a| a < 4}   #=> [4, 5, 6]
arr                         #=> [4, 5, 6]

arr = [1, 2, 3, 4, 5, 6]
arr.keep_if {|a| a < 4}   #=> [1, 2, 3]
arr                       #=> [1, 2, 3]
```

## What's Here

關於 Array 這個 class, 有些東西是在別的地方說明：

- 從 [Object](https://ruby-doc.org/core-3.1.2/Object.html#class-Object-label-What-27s+Here) 這個 class 繼承的東西
- Class Array Includes [module Enumerable](https://ruby-doc.org/core-3.1.2/Enumerable.html#module-Enumerable-label-What-27s+Here), 它提供了許多額外的方法。

這裡，class Array 提供一些 methods 用於：

- 建立 Array
- 查詢
- 比較
- 獲取
- Assigning (賦值)
- 刪除
- 組合
- Iterating (遍歷，迭代)
- 轉換
- 更多

### 用於建立 Array 的方法

| method | 說明 |
| ------ | ---- |
| [`::[]`](https://ruby-doc.org/core-3.1.2/Array.html#method-c-5B-5D)   | 回傳：使用給定物件填充的新陣列 |
| [::new](https://ruby-doc.org/core-3.1.2/Array.html#method-c-new)  | 回傳一個新的陣列 |
| [::try_convert](https://ruby-doc.org/core-3.1.2/Array.html#method-c-try_convert) | 回傳一個由給定物件轉換而來的新陣列 |

### 用於查詢的方法

| method | 說明 |
| ------ | ---- |
| [length](https://ruby-doc.org/core-3.1.2/Array.html#method-i-length), [size](https://ruby-doc.org/core-3.1.2/Array.html#method-i-size) | 回傳元素的數量。 |
| [include?](https://ruby-doc.org/core-3.1.2/Array.html#method-i-include-3F) | 回傳是否有任何元素 == 給定的物件。 |
| [empty?](https://ruby-doc.org/core-3.1.2/Array.html#method-i-empty-3F) | 回傳是否沒有任何元素。 |
| [all?](https://ruby-doc.org/core-3.1.2/Array.html#method-i-all-3F) | 回傳是否所有元素都符合給定的條件。 |
| [any?](https://ruby-doc.org/core-3.1.2/Array.html#method-i-any-3F) | 回傳是否有任何元素符合給定的條件。 |
| [none?](https://ruby-doc.org/core-3.1.2/Array.html#method-i-none-3F) | 回傳是否沒有任何元素 == 給定的物件。 |
| [one?](https://ruby-doc.org/core-3.1.2/Array.html#method-i-one-3F) | 回傳是否正好只有一個元素 == 給定的物件。 |
| [count](https://ruby-doc.org/core-3.1.2/Array.html#method-i-count) | 回傳符合條件的元素數量 |
| [find_index](https://ruby-doc.org/core-3.1.2/Array.html#method-i-find_index), [index](https://ruby-doc.org/core-3.1.2/Array.html#method-i-index) | 回傳符合條件的第一個元素的 index |
| [rindex](https://ruby-doc.org/core-3.1.2/Array.html#method-i-rindex) | 回傳符合條件的最後一個元素的 index |
| [hash](https://ruby-doc.org/core-3.1.2/Array.html#method-i-hash) | 回傳一個 integer 是這個陣列的 hash code. |

### 用於比較的方法

| method | 說明 |
| ------ | ---- |
| [#<=>](https://ruby-doc.org/core-3.1.2/Array.html#method-i-3C-3D-3E) | 回傳 -1, 0, 或 1，表示 self 小於、等於、或大於 給定的物件。 |
| [#==](https://ruby-doc.org/core-3.1.2/Array.html#method-i-3D-3D) | 回傳：self 中的每個元素是否 == 給定物件中的相應元素。 |
| [eql?](https://ruby-doc.org/core-3.1.2/Array.html#method-i-eql-3F) | 回傳：self 中的每個元素是否是 eql? 給定物件中的相應元素。 |

### 用於 獲取(fetching) 的方法

這些方法不會改變自己：

| method | 說明 |
| ------ | ---- |
| [`[]`](https://ruby-doc.org/core-3.1.2/Array.html#method-i-5B-5D) | 或傳一個或多個元素 | 
| [fetch](https://ruby-doc.org/core-3.1.2/Array.html#method-i-fetch) | 回傳給定位置(offset)的元素 |
| [first](https://ruby-doc.org/core-3.1.2/Array.html#method-i-first) | 回傳第一個或開頭的幾個元素 |
| [last](https://ruby-doc.org/core-3.1.2/Array.html#method-i-last) | 回傳最後一個或倒數幾個元素 |
| [max](https://ruby-doc.org/core-3.1.2/Array.html#method-i-max) | 回傳一個或多個值為最大的元素，根據 `<=>` 或給定的 block 來決定大小。 |
| [min](https://ruby-doc.org/core-3.1.2/Array.html#method-i-min) | 回傳一個或多個值為最小的元素，根據 `<=>` 或給定的 block 來決定大小。 |
| [minmax](https://ruby-doc.org/core-3.1.2/Array.html#method-i-minmax) | 回傳最小值以及最大值，，根據 `<=>` 或給定的 block 來決定大小。 |
| [assoc](https://ruby-doc.org/core-3.1.2/Array.html#method-i-assoc) | 回傳第一個符合的元素，這個元素也是個陣列，而且它的第一個元素 == 給定的物件 |
| [rassoc](https://ruby-doc.org/core-3.1.2/Array.html#method-i-rassoc) | 回傳第一個符合的元素，這個元素也是個陣列，而且它的第二個元素 == 給定的物件 |
| [at](https://ruby-doc.org/core-3.1.2/Array.html#method-i-at) | 回傳給定位置(offset)的元素 |
| [values_at](https://ruby-doc.org/core-3.1.2/Array.html#method-i-values_at) | 根據給定的位置、多個位置或位置範圍，回傳符合的元素 |
| [dig](https://ruby-doc.org/core-3.1.2/Array.html#method-i-dig) | 根據給定的 index 以及額外的參數，回傳多層套疊(nested)的物件中的物件 |
| [drop](https://ruby-doc.org/core-3.1.2/Array.html#method-i-drop) | 回傳：去掉前面 n 個元素之後的元素 |
| [take](https://ruby-doc.org/core-3.1.2/Array.html#method-i-take) | 回傳前面 n 個元素 |
| [drop_while](https://ruby-doc.org/core-3.1.2/Array.html#method-i-drop_while) | 根據給定的 block, 丟掉(drop)前面符合條件的元素，回傳後面的元素。 |
| [take_while](https://ruby-doc.org/core-3.1.2/Array.html#method-i-take_while) | 根據給定的 block, 回傳前面符合條件的元素。 |
| [slice](https://ruby-doc.org/core-3.1.2/Array.html#method-i-slice) | 根據給定的參數，回傳連續的元素 |
| [sort](https://ruby-doc.org/core-3.1.2/Array.html#method-i-sort) | 回傳：根據 `<=>` 或給定的 block 排序過的所有元素。 |
| [reverse](https://ruby-doc.org/core-3.1.2/Array.html#method-i-reverse) | 回傳反向排序的所有元素 |
| [compact](https://ruby-doc.org/core-3.1.2/Array.html#method-i-compact) | 回傳一個陣列包含所有不是 nil 的元素 |
| [select](https://ruby-doc.org/core-3.1.2/Array.html#method-i-select), [filter](https://ruby-doc.org/core-3.1.2/Array.html#method-i-filter) | 回傳一個陣列包含所有根據給定的 block 所選取的元素 |
| [uniq](https://ruby-doc.org/core-3.1.2/Array.html#method-i-uniq) | 回傳一個陣列包含去重複之後的元素 |
| [rotate](https://ruby-doc.org/core-3.1.2/Array.html#method-i-rotate) | 回傳所有元素，但是某些元素由某一端旋轉至另一端。 |
| [bsearch](https://ruby-doc.org/core-3.1.2/Array.html#method-i-bsearch) | 透過給定的 block，回傳經由 二分搜尋法(binary search) 所選定的元素 |
| [bsearch_index](https://ruby-doc.org/core-3.1.2/Array.html#method-i-bsearch_index) | 透過給定的 block，回傳經由 二分搜尋法(binary search) 選定元素的 index |
| [sample](https://ruby-doc.org/core-3.1.2/Array.html#method-i-sample) | 自陣列中隨機選取一個或多個元素回傳 |
| [shuffle](https://ruby-doc.org/core-3.1.2/Array.html#method-i-shuffle) | 洗牌，以隨機次序回傳所有元素 |

### 用於賦值(Assign)的方法

這些方法會增加、取代或排序 self 裡的元素。

| method | 說明 |
| ------ | ---- |
| [`[]=`](https://ruby-doc.org/core-3.1.2/Array.html#method-i-5B-5D-3D) | 將給定的物件 assign 給某個特定元素 |
| [push](https://ruby-doc.org/core-3.1.2/Array.html#method-i-push), [append](https://ruby-doc.org/core-3.1.2/Array.html#method-i-append), [`<<`](https://ruby-doc.org/core-3.1.2/Array.html#method-i-3C-3C) | 在陣列後面新增元素 |
| [unshift](https://ruby-doc.org/core-3.1.2/Array.html#method-i-unshift), [prepend](https://ruby-doc.org/core-3.1.2/Array.html#method-i-prepend) | 在陣列前面新增元素 |
| [insert](https://ruby-doc.org/core-3.1.2/Array.html#method-i-insert) | 在指定的位置插入某些給定的物件；不會取代原有的元素 |
| [concat](https://ruby-doc.org/core-3.1.2/Array.html#method-i-concat) | 將給定陣列內的元素新增至 self 最後面 |
| [fill](https://ruby-doc.org/core-3.1.2/Array.html#method-i-fill) | 將指定的元素取代為指定的物件 |
| [replace](https://ruby-doc.org/core-3.1.2/Array.html#method-i-replace) | 將 self 的內容取代為給定陣列的內容 |
| [reverse!](https://ruby-doc.org/core-3.1.2/Array.html#method-i-reverse-21) | 將 self 的內容以相反次序的元素取代 |
| [rotate!](https://ruby-doc.org/core-3.1.2/Array.html#method-i-rotate-21) | 將 self 的內容以旋轉後的元素取代 |
| [shuffle!](https://ruby-doc.org/core-3.1.2/Array.html#method-i-shuffle-21) | 將 self 的內容取代為隨機排序後的元素 |
| [sort!](https://ruby-doc.org/core-3.1.2/Array.html#method-i-sort-21) | 將 self 取代為排序後的元素，排序規則由 <=> 或給定的 block 決定。 |
| [sort_by!](https://ruby-doc.org/core-3.1.2/Array.html#method-i-sort_by-21) | 將 self 取代為排序後的元素，排序規則由給定的 block 決定。 |

### 用於刪除的方法

下列的每一個方法都會從 self 裡移除元素：

| method | 說明 |
| ------ | ---- |
| [pop](https://ruby-doc.org/core-3.1.2/Array.html#method-i-pop) | 移除並回傳 最後一個元素 |
| [shift](https://ruby-doc.org/core-3.1.2/Array.html#method-i-shift) | 移除並回傳 第一個元素 |
| [compact!](https://ruby-doc.org/core-3.1.2/Array.html#method-i-compact-21) | 移除 所有 nil 元素 |
| [delete](https://ruby-doc.org/core-3.1.2/Array.html#method-i-delete) | 移除 所有 與給定物件相同的元素 |
| [delete_at](https://ruby-doc.org/core-3.1.2/Array.html#method-i-delete_at) | 移除 指定位置的元素 |
| [delete_if](https://ruby-doc.org/core-3.1.2/Array.html#method-i-delete_if) | 移除 給定的 block 所指定的元素 |
| [keep_if](https://ruby-doc.org/core-3.1.2/Array.html#method-i-keep_if) | 保留 給定的 block 所指定的元素，其他的都移除 |
| [reject!](https://ruby-doc.org/core-3.1.2/Array.html#method-i-reject-21) | 移除 給定的 block 所指定的元素 |
| [select!](https://ruby-doc.org/core-3.1.2/Array.html#method-i-select-21), [filter!](https://ruby-doc.org/core-3.1.2/Array.html#method-i-filter-21) | 保留 給定的 block 所指定的元素，其他的都移除 |
| [slice!](https://ruby-doc.org/core-3.1.2/Array.html#method-i-slice-21) | (切片) 移除一部份連續位置的元素 |
| [uniq!]() | 移除重複 |

### 用於結合的方法

| method | 說明 |
| ------ | ---- |
| [#&](https://ruby-doc.org/core-3.1.2/Array.html#method-i-26) | 回傳一個陣列，內容是 self 與給定陣列的交集 | 
| [intersection](https://ruby-doc.org/core-3.1.2/Array.html#method-i-intersection) | 回傳一個陣列，內容是 self 與給定多個陣列的交集 | 
| [+](https://ruby-doc.org/core-3.1.2/Array.html#method-i-2B) | 回傳一個陣列，包含 self 的所有元素，後面跟著給定陣列的所有元素。 |
| [-](https://ruby-doc.org/core-3.1.2/Array.html#method-i-2D) | 回傳一個 slef 的副本，去除出現在給定陣列中的元素。 |
| [#|](https://ruby-doc.org/core-3.1.2/Array.html#method-i-7C) | 回傳一個陣列是 self 和 給定陣列的聯集 (相加去重複)。 |
| [union](https://ruby-doc.org/core-3.1.2/Array.html#method-i-union) | 回傳一個陣列是 self 和 給定多個陣列的聯集 |
| [difference](https://ruby-doc.org/core-3.1.2/Array.html#method-i-difference) | 回傳一個 slef 的副本，去除出現在給定陣列中的元素。 |
| [product](https://ruby-doc.org/core-3.1.2/Array.html#method-i-product) | self 的每個元素 與 給定一個或多個陣列的每個元素的所有各種組合，回傳一個陣列包含所有這些組合，或是每種組合執行(yield)一個 block |

### 用於 Iterating(遍歷) 的方法

| method | 說明 |
| ------ | ---- |
| [each](https://ruby-doc.org/core-3.1.2/Array.html#method-i-each) | 將每個元素傳遞給 block |
| [reverse_each](https://ruby-doc.org/core-3.1.2/Array.html#method-i-reverse_each) | 以相反的次序，將每個元素傳遞給 block |
| [each_index](https://ruby-doc.org/core-3.1.2/Array.html#method-i-each_index) | 將每個元素的 index 傳遞給 block |
| [cycle](https://ruby-doc.org/core-3.1.2/Array.html#method-i-cycle) | 使用每個元素呼叫給定的 block，然後重複執行指定的次數，或是一直執行。 |
| [combination](https://ruby-doc.org/core-3.1.2/Array.html#method-i-combination) | 從 self 裡的元素取 n 個元素的各種組合，呼叫給定的 block；一個組合裡，相同的元素最多出現一次。 |
| [permutation](https://ruby-doc.org/core-3.1.2/Array.html#method-i-permutation) | 從 self 裡的元素取 n 個元素的各種[permutation(排列或置換)](https://zh.wikipedia.org/zh-tw/%E7%BD%AE%E6%8F%9B)，呼叫給定的 block；一個排列裡，相同的元素最多出現一次。 |
| [repeated_combination](https://ruby-doc.org/core-3.1.2/Array.html#method-i-repeated_combination) | 從 self 裡的元素取 n 個元素的各種組合，呼叫給定的 block；一個組合裡，相同的元素可以出現多次。 |
| [repeated_permutation](https://ruby-doc.org/core-3.1.2/Array.html#method-i-repeated_permutation) | 從 self 裡的元素取 n 個元素的各種排列，呼叫給定的 block；一個排列裡，相同的元素可以出現多次。 |

### 用於轉換的方法

| method | 說明 |
| ------ | ---- |
| [map](https://ruby-doc.org/core-3.1.2/Array.html#method-i-map), [collect](https://ruby-doc.org/core-3.1.2/Array.html#method-i-collect) | 每個元素呼叫 block 的回傳值，組成一個新的陣列回傳 |
| [map!](https://ruby-doc.org/core-3.1.2/Array.html#method-i-map-21), [collect!](https://ruby-doc.org/core-3.1.2/Array.html#method-i-collect-21) | 將每個元素取代為呼叫 block 之後的回傳值 |
| [flatten](https://ruby-doc.org/core-3.1.2/Array.html#method-i-flatten) | 回傳一個新的陣列是 self 遞迴平坦化後的結果 |
| [flatten!](https://ruby-doc.org/core-3.1.2/Array.html#method-i-flatten-21) | 將 self 裡的每個多層套疊的(nested)陣列，取代為該陣列裡的元素 |
| [inspect](https://ruby-doc.org/core-3.1.2/Array.html#method-i-inspect), [to_s](https://ruby-doc.org/core-3.1.2/Array.html#method-i-to_s) | 將 self 轉成一個字串表達形式 |
| [join](https://ruby-doc.org/core-3.1.2/Array.html#method-i-join) | 將 self 裡的每個元素轉成字串，然後使用指定的連接符(separator)連接成一個新字串回傳 |
| [to_a](https://ruby-doc.org/core-3.1.2/Array.html#method-i-to_a) | 回傳 self 或一個新陣列 |
| [to_ary](https://ruby-doc.org/core-3.1.2/Array.html#method-i-to_ary) | 回傳 self |
| [to_h](https://ruby-doc.org/core-3.1.2/Array.html#method-i-to_h) | 回傳一個新的 hash, 由 self 的元素形成 |
| [transpose](https://ruby-doc.org/core-3.1.2/Array.html#method-i-transpose) | 將多維陣列的 欄、列 對調 |
| [zip](https://ruby-doc.org/core-3.1.2/Array.html#method-i-zip) | 將多個陣列組成一個多維陣列 |

### 其他方法

| method | 說明 |
| ------ | ---- |
| [*](https://ruby-doc.org/core-3.1.2/Array.html#method-i-2A) | 如果參數是 ingeter，就將 self 重複 n 次；如果參數是字串，等同呼叫 join |
| [abbrev](https://ruby-doc.org/stdlib-3.1.2/libdoc/abbrev/rdoc/Abbrev.html) | 回傳一個 hash，內容是 self 內所有元素的無歧義縮寫 |
| [pack](https://ruby-doc.org/core-3.1.2/Array.html#method-i-pack) | 將 self 內的元素打包成二進制序列 |
| [sum](https://ruby-doc.org/core-3.1.2/Array.html#method-i-sum) | 根據 + 或給定的 block 回傳元素的總和。 |
