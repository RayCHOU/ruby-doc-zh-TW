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
