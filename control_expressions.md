# Control Expressions (控制 表示式)

參考 Ruby-Doc => [Control Expressions](https://ruby-doc.org/core-3.1.2/doc/syntax/control_expressions_rdoc.html)

Ruby 有多種方式可以控制程式的執行。這裡所描述的所有 expressions (表達式；表示式；運算式；算式) 都會回傳一個值。

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

從 Ruby 2.7 開始，`case` expression 還通過 `in` 關鍵字提供了更強大的實驗「模式匹配」(pattern matching) 功能：

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

## `while` 迴圈

當條件為真，`while` 迴圈會一直執行。

```ruby
a = 0

while a < 10 do
  p a
  a += 1
end

p a
```

這樣會印出 0 到 10. 
進入迴圈之前會先檢查 `a < 10` 這個條件，然後執行本體 (body)，然後再次檢查條件。
如果條件結果是 false，這個循環就會結束。

`do` 這個關鍵可有可無。
下面這個迴圈的效果跟上面一樣：

```ruby
while a < 10
  p a
  a += 1
end
```

`while` 迴圈的結果是 `nil`，除非使用 `break` 提供某個值。

## `until` 迴圈

當條件為 false，`until` 迴圈會一直執行。

```ruby
a = 0

until a > 10 do
  p a
  a += 1
end

p a
```

這樣會印出 0 到 11.
跟 `while` 迴圈一樣，進入迴圈前以及每次執行迴圈本體前會先檢查 `a > 10` 這個條件。
如果條件是 false 就會繼續執行。

跟 `while` 迴圈一樣，`do` 是可有可無的。

跟 `while` 迴圈一樣，`until` 迴圈的結果是 `nil`，除非使用 `break`。

## `for` 迴圈

`for` 迴圈的組成是 `for` 後跟著一個變數，這個變數用來存放迭代參數 (iteration argument)，後面再跟著 `in`，後面再跟著要被循環迭代 (iterate over) 的值。
`do` 是可有可無的：

```ruby
for value in [1, 2, 3] do
  puts value
end
```

這樣會印出 1、2、3.

跟 `while` 和 `until` 一樣，`do` 是可有可無的。

`for` 迴圈跟使用 `each` 類似，但是不會建立新的「變數有效範圍」。

`for` 迴圈的結果值是「被循環迭代 (iterated over) 的值」，除非使用 `break`。

在現代 ruby 程式裡很少使用 `for` 迴圈。

## Modifier (修飾符) `while` 和 `until`

跟 `if` 和 `unless` 一樣，`while` 和 `until` 也可以被當做 modifier 使用：

```ruby
a = 0

a += 1 while a < 10

p a # prints 10
```

`until` 用做 modifier：

```ruby
a = 0

a += 1 until a > 10

p a # prints 11
```

你可以用 `begin` 和 `end` 來建立 `while` 迴圈，這樣迴圈本體在條件檢查前至少會執行一次：

```ruby
a = 0

begin
  a += 1
end while a < 10

p a # prints 10
```

如果你沒有使用 `rescue` 或 `ensure`，Ruby 會進行優化 (optimize)，去除不必要的「例外處理」成本。

## `break` Statement

使用 `break` 提早離開某個 block。下面的例子中，如果 values 裡有某個是偶數，就會中斷迴圈：

```ruby
values.each do |value|
  break if value.even?

  # ...
end
```

您也可以使用 `break` 來終止一個 `while` 迴圈：

```ruby
a = 0

while true do
  p a
  a += 1

  break if a < 10
end

p a
```

這樣會印出 0 跟 1.

`break` 接受某個值作為被它中斷的 expression 的結果值：

```ruby
result = [1, 2, 3].each do |value|
  break value * 2 if value.even?
end

p result # 印出 4
```

## `next` Statement

使用 `next` 跳過當前迭代 (iteration) 的其餘部分：

```ruby
result = [1, 2, 3].map do |value|
  next if value.even?

  value * 2
end

p result # 印出 [2, nil, 6]
```

`next` 接受某個參數做為當前迭代的結果值：

```ruby
result = [1, 2, 3].map do |value|
  next value if value.even?

  value * 2
end

p result # prints [2, 2, 6]
```

## `redo` Statement

使用 `redo` 再做一次當前迭代 (iteration):

```ruby
result = []

while result.length < 10 do
  result << result.length

  redo if result.last.even?

  result << result.length + 1
end

p result
```

這會印出： [0, 1, 3, 3, 5, 5, 7, 7, 9, 9, 11]

## Modifier Statements

Ruby 的語法會區分 statements 和 expressions。所有 expressions 都是 statements（一個 expression 是 statement 的一種類型），但並非所有 statements 都是 expressions。語法的某些部分接受 expressions 而不接受其他類型的 statements，這導致看起來相似的程式碼被不同地解析。

例如，當不是做為 modifier 的時候，`if`, `else`, `while`, `until`, 和 `begin` 是 expressions (也是 statements)。然而，當它們做為 modifier 使用的時候，`if`, `else`, `while`, `until` 和 `rescue` 是 statements 但不是 expressions。

```ruby
if true; 1 end # expression (and therefore statement)
1 if true      # statement (not expression)
```

不是 expressions 的 statements 不能在需要 expression 的上下文中使用，例如 method 的參數。

```ruby
puts( 1 if true )    #=> SyntaxError
```

你可以把 statement 用小括號括起來，這樣會建立一個 expression.

```ruby
puts((1 if true))    #=> 1
```

如果你放個空格在 method 名稱跟左括號之間，你就不需要兩套括號。

```ruby
puts (1 if true)    #=> 1, 因為 method 的小括號可以省略
```

這是因為它的解析類似於不帶括號的方法呼叫。它的效果跟以下程式碼相同，只差無需創建區域變數：

```ruby
x = (1 if true)
p x
```

在一個 modifier statement 裡面，左側必須是 statement，右側必須是 expression。

所以在 `a if b rescue c` 裡面，因為 `b rescue c` 是一個不是 expression 的 statement，因此不允許作為 `if` modifier statement 的右側，程式碼必然被解析為 `(a if b) rescue c`。

這與「運算符優先序」的交互方式如下：

```ruby
stmt if v = expr rescue x
stmt if v = expr unless x
```

會被解析為：

```ruby
stmt if v = (expr rescue x)
(stmt if v = expr) unless x
```

這是因為修飾符 `rescue` 的優先級高於 `=`，而修飾符 `if` 的優先級低於 `=`。

## Flip-Flop (正反器、觸發器)

Flip-Flop 是一種很少見的條件表達式 (conditional expression)。它的主要用途是處理與 `ruby​​ -n` 或 `ruby​​ -p` 一起使用的 ruby​​ 單行程序的文本。

Flip-Flop 的形式是：先是一個表示 flip-flop 何時變成 on 的 expression，然後接著 `..`（或 `...`），然後是表示 flip-flop 何時變成 off 的 expression。當 flip-flop 是 on，它將繼續評估為 `true`，off 時為 `false`。

這裡是個例子：

```ruby
selected = []

0.upto 10 do |value|
  selected << value if value==2..value==8
end

p selected # prints [2, 3, 4, 5, 6, 7, 8]
```

在上面的例子中，on 的條件是 `n==2`。Flip-flop 最初在 0 和 1 時為 off（false），但在 2 時變為 on（true）並保持打開到 8。在 8 之後它 off，並在 9 和 10 中保持 off。

Flip-flop 必須在條件語句中使用，例如 `if`、`while`、`unless`、`until` 等，包括修飾符形式。

當您使用包含範圍 (inclusive range) (..) 時，會在 on 條件更改時評估 off 條件：

```ruby
selected = []

0.upto 5 do |value|
  selected << value if value==2..value==2
end

p selected # prints [2]
```

在這裡，flip-flop 的兩側都被評估，因此 flip-flop 僅在 `value` 等於 2 時打開和關閉。由於 flip-flop 在迭代 (interation) 中打開，它回傳 true。

當您使用排外範圍 (exclusive range) (...) 時，將在下一個迭代中評估關閉條件：

```ruby
selected = []

0.upto 5 do |value|
  selected << value if value==2...value==2
end

p selected # prints [2, 3, 4, 5]
```

這裡，flip-flop 在 `value` 等於 2 時打開，但不會在同一次迭代中關閉。直到下一次迭代才評估關閉條件，並且 `value` 永遠不會再為 2。
