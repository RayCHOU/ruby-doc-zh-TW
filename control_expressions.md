# Control Expressions (控制 表示式)

參考 Ruby-Doc => [Control Expressions](https://ruby-doc.org/core-3.1.2/doc/syntax/control_expressions_rdoc.html)

Ruby 有多種方式可以控制程式的執行。這裡所描述的所有 expressions (表示式) 都會回傳一個值。

在這些 control expressions 裡的測試條件，`nil` 跟 `false` 是 false-values (求值結果為假)，其他所有物件都是 true-values (求值結果為真)。在這份文件裡，true 就表示 true-value，false 就表示 false-value。

## `if` Expression

最簡單的 if expression 有兩部分，一個 "test" expression (條件測試) 以及一個 "then" expression。如果 "test" expression 的結果是 true，就會執行 "then" expression。

這是個簡單的 if statement:

```ruby
if true then
  puts "測試結果為真"
end
```

這樣會印出「測試結果為真」。

`then` 可以省略：

```ruby
if true
  puts "測試結果為真"
end
```

這份文件裡所有的 expressions 都會省略 `then`，因為這是 `if` 最常見的用法。

你也可以加一個 `else` expression。如果測試的結果不是 true，`else` expression 就會被執行。

```ruby
if false
  puts "測試結果為真"
else
  puts "測試結果為假"
end
```

這樣會印出「測試結果為假」。

你可以使用 `elsif` 為 if expression 增加任意數量的額外測試。當某個 `elsif` 之前的測試結果都是 false，這個 `elsif` 就會被執行。

```ruby
a = 1

if a == 0
  puts "a 是 0"
elsif a == 1
  puts "a 是 1"
else
  puts "a 是某個其他的值"
end
```

這樣會印出「a 是 1」，因為 1 不等於 0，而 `else` 只有當所有條件都不符合時才會執行。

一旦某個條件符合，不管是 `if` 的條件或任何 `elsif` 條件，這個 `if` expression 就完成了，不會再做任何測試。

跟 `if` 一樣，`elsif` 條件後面也可以跟一個 `then`。

這個例子只會印出「a 是 1」：

```ruby
a = 1

if a == 0
  puts "a 是 0"
elsif a == 1
  puts "a 是 1"
elsif a >= 1
  puts "a 大於等於 1"
else
  puts "a 是其他值"
end
```

`if` 跟 `elsif` 的測試條件可能會有副作用(side-effects)。副作用最常見的用法是把某個值存到區域變數：

```ruby
if a = object.some_value
  # do something to a
end
```

`if` expression 的回傳值是這個 expression 裡最後被執行的結果。

## Ternary if (三元 if)

你也可以使用問號 ? 以及冒號 : 來寫 if-then-else expression. 下面這個「三元 if」(ternary if):

```ruby
input_type = gets =~ /hello/i ? "greeting" : "other"
```

跟下面這個 `if` expression 是一樣的：

```ruby
input_type =
  if gets =~ /hello/i
    "greeting"
  else
    "other"
  end
```

雖然「三元 if」簡短得多，但為了便於閱讀，建議「三元 if」僅用於簡單的條件。此外，避免在同一個 expression 中使用多個三元條件，因為這可能會造成混淆。

## `unless` Expression

`unless` expression 是 `if` expression 的相反。如果值是 false，`then` expression 就會被執行：

```ruby
unless true
  puts "值為假"
end
```

這不會印出東西，因為 true 不是一個 false-value.

跟 `if` 一樣，`unless` 也可以使用 `then`.

注意上面的 `unless` expression 跟下面一樣：

```ruby
if not true
  puts "值為假"
end
```

跟 `if` expression 一樣，你也可以在 `unless` 裡使用 `else`:

```ruby
unless true
  puts "值為假"
else
  puts "值為真"
end
```

這樣會從 `else` 條件裡印出「值為真」。

你不能在 `unless` expression 裡使用 `elsif`。

`unless` expression 的結果值是這個 expression 裡最後執行的結果。

## Modifier (修飾符) `if` 和 `unless`

`if` 和 `unless` 也可以用來修飾一個 expression。
當它們作為修飾符使用的時候，左手邊是 "then" statement，右手邊是 "test" expression:

```ruby
a = 0

a += 1 if a.zero?

p a
```

這樣會印出 1.

```ruby
a = 0

a += 1 unless a.zero?

p a
```

這樣會印出 0.

雖然修飾符和標準版本都有 "test" expression 和 "then" statement，但由於 parse 的順序，它們並不是彼此的精確轉換。這個例子可以看出兩者的不同：

```ruby
p a if a = 0.zero?
```

這會引發 `NameError` "undefined local variable or method `a'".

當 ruby 在 parse 這個 expression 的時候，它先遇到 "then" expression 裡的 a，並把它當做是一個 method 呼叫。然後它在 "test" expression 裡看到一個 assignment 賦值到 a，並把 a 標示為區域變數。

然後在執行這行的時候，它先執行 "test" expression, a = 0.zero?.

因為 test 的結果是 true，所以接著執行 "then" expression: `p a`. 由於 body 裡的 a 被記錄為一個 method，而它並不存在，於是就引發了 `NameError`。

這情況在 `unless` 也是一樣。

## `case` Expression

`case` expression 有兩種使用方式。

最常見的方法是將一個 object 與多個 pattern 進行比較。
這些 patterns 會使用 `===` 方法做匹配，這個方法在 Object 這個 class 裡是 `==` 的別名。
其他 classes 必須覆蓋它以提供有意義的行為。有關示例，請參見 [`Module#===`](https://ruby-doc.org/core-3.1.2/Module.html#method-i-3D-3D-3D) 和 [`Regexp#===`](https://ruby-doc.org/core-3.1.2/Regexp.html#method-i-3D-3D-3D)。

下面是一個使用 `case` 將 `String` 與 pattern 進行比較的示例：

```ruby
case "12345"
when /^1/
  puts "字串的開頭是 1"
else
  puts "我不知道字串的開頭是什麼"
end
```

這裡通過呼叫 `/^1/ === "12345"` 將字符 `"12345"` 與 `/^1/` 進行比較，回傳 true。
跟 `if` expression 一樣，第一個匹配的 `when` 會被執行，其他所有匹配都會忽略。

如果都沒有匹配，就會執行 `else`.

`else` 跟 `then` 可以不要，下面這個 `case` expression 跟上面的結果一樣：

```ruby
case "12345"
when /^1/
  puts "字串的開頭是 1"
end
```

同一條 `when` 裡面可以有多個條件：

```ruby
case "2"
when /^1/, "2"
  puts "字串開頭是 1 或者它是 '2'"
end
```

Ruby 會依次嘗試每個條件，所以首先 `/^1/ === "2"` 回傳 `false`，然後 `"2" === "2"` 回傳 true，所以會印「字串開頭是 1 或者它是 '2'」。

您也可以在 `when` 條件之後使用 `then`。這最常用於將 `when` 的主體放在同一行上。

```ruby
case a
when 1, 2 then puts "a 是 1 或 2"
when 3    then puts "a 是 3"
else           puts "我不知道 a 是什麼"
end
```

使用 `case` expression 的另一種方式類似於 if-elsif expression：

```ruby
a = 2

case
when a == 1, a == 2
  puts "a 是 1 或 2"
when a == 3
  puts "a 是 3"
else
  puts "我不知道 a 是什麼"
end
```

一樣，`then` 跟 `else` 不一定要。

一個 `case` expression 的結果值是最後一個被執行的結果值。

從 Ruby 2.7 開始，`case` expression 還通過 `in` 關鍵字提供了更強大的實驗 pattern 匹配功能：

```ruby
case {a: 1, b: 2, c: 3}
in a: Integer => m
  "matched: #{m}"
else
  "not matched"
end
# => "matched: 1"
```

Pattern matching 語法描述在[這裡](https://ruby-doc.org/core-3.1.2/doc/syntax/pattern_matching_rdoc.html)。
