# Regexp

https://ruby-doc.org/core-3.1.2/Regexp.html 

Regular expressions (正規表示式, regexps) 是用來描述字串內容的 patterns.  
它們用來檢視字串內容是否包含某個 pattern, 或者取出符合的部分。

Regexp 是一個 class, 它用來在字串中找到符合的 pattern.  
Regexps 可以使用 `/.../` 以及 `%r{...}` 語法來建立，也可以使用 `Regexp::new` constructor(構造方法, 建構子).

## Regexp Literals

https://ruby-doc.org/core-3.1.2/doc/syntax/literals_rdoc.html#label-Regexp+Literals

使用斜線 “/” 建立 regular expression:

```ruby
/my regular expression/
```

後面可以跟著 flag, 例如 m 表示 multiline mode (多行模式)

```ruby
/my regular expression/m
```

### Interpolation

regular expressions 裡面也可以使用 interpolation 以及 escaped characters

```ruby
tag = 'p'
/\n<#{tag}/
```

### %r

當 regular expressions 裡面有斜線字元 / 的時候，可以使用 %r
```ruby
%r{^/blog/2011/(.*)$}
```

## =~

字串與 regexp 可以對調：

```ruby
/hay/ =~ 'haystack'   #=> 0
'haystack' =~ /hay/   #=> 0
/a/   =~ 'haystack'   #=> 1
/u/   =~ 'haystack'   #=> nil
```

## Metacharacters and Escapes

https://ruby-doc.org/core-3.1.2/Regexp.html#class-Regexp-label-Metacharacters+and+Escapes 

metacharacters 有這些：()[]{}.?+*  
它們在 pattern 裡有特殊的意義。  
如果要 match 它們的表面文字，就要用 反斜線(backslash) 做 escape. 
比如說，要找「1 + 2 = 3?」這樣的 pattern:

```ruby
/1 \+ 2 = 3\?/.match('Does 1 + 2 = 3?')
```

## Character Classes

https://ruby-doc.org/core-3.1.2/Regexp.html#class-Regexp-label-Character+Classes 

一個 character class 是用一對中括號，裡面列出允許的字元。  
`/[ab]/` 表示 a or b

### range

character class 裡面的 hyphen (-) 是個 metacharacter 表示某個字元範圍 (inclusive range of characters)。  
例如 `[abcd]` 跟 `[a-d]` 的效果是一樣的。

range 後面可以再跟另一個 range,  
例如 `[abcdwxyz]` 跟 `[a-dw-z]` 的效果是一樣的。

character class 裡的字元或 range 的順序是無關緊要的。

### ^ (invert)

如果 character class 的第一個字元是 caret (^)，表示這個 class 要反過來：除了這些字元以外。

```ruby
/[^a-eg-z]/.match('f') #=> #<MatchData "f">
```

### metacharacters 

下列 metacharacters 的作用也類似 character classes:

|     |                                 | 等效           |
| --- | ------------------------------- | ------------- |
| /./ | 換行以外的任意字元                 |               |
| /./m | 任意字元 (m 表示 multiline mode) |               |
| /\d/ | 數字                            | [0-9]         |
| /\D/ | 非數字                          | [^0-9]         |
| /\s/ | A whitespace character         | [ \t\r\n\f\v]  |
| /\S/ | A non-whitespace character.    | [^ \t\r\n\f\v] |

## Repetition

https://ruby-doc.org/core-3.1.2/Regexp.html#class-Regexp-label-Repetition 

某個單一字元後面可以跟著一個 repetition metacharacter 來指定這個字元要重複幾次。  
這些 metacharacters 稱為 quantifiers.

|       | 說明 |
| ----- | ------- |
| *     | 零或多次 |
| +     | 一或多次 |
| ?     | 零或一次 (optional) |
| {n}   | 必須剛好重複 n 次    |
| {,m}  | 最大次數 m 或更少次  |
| {n,m} | 最少 n 次，最多 m 次 |

### Greedy match

重複次數 預設 貪心原則 (greedy)：在成功 match 的前提下，儘可能 match 更多次。

相反的，lazy 是 match 最少次數。  
大部分的 greedy metacharacters 可以在後面加個問號 ? 變成 lazy.  
除了 {n} 這個 pattern, 因為它明確指定重複次數要剛好是 n 次，次數是不可變的, 加上 ? 就變成 optional，也就是「零或 n 次」。

以下兩個 pattern 都會 match.  
第一個是 greedy 所以 `.+` 會 matches `"<a><b>"`;  
第二個使用 lazy quantifier 所以 `.+?` 會 matche `"<a>"`:

```ruby
/<.+>/.match("<a><b>")  #=> #<MatchData "<a><b>">
/<.+?>/.match("<a><b>") #=> #<MatchData "<a>">
```
  
## Capturing

https://ruby-doc.org/core-3.1.2/Regexp.html#class-Regexp-label-Capturing 

小括號可用於捕捉 (capturing)。  
由第 n 組小括號括起來的文本隨後可以用 n 引用。  
在 pattern 裡面模式中使用 backreference(反向參考) \n（例如 \1）；  
在 pattern 之外使用 MatchData[n]（例如 MatchData[1]）。

例如以下程式 match AABB 式疊詞：
```ruby
/(.)\1(.)\2/.match("尋尋覓覓，冷冷清清") #=> #<MatchData "尋尋覓覓" 1:"尋" 2:"覓">
```

上面程式 \1 指向第一個 group, \2 指向第二個 group.

`Regexp#match` 回傳 MatchData 物件，使用它的 [] method 可以取得捕捉到的內容：

```ruby
s = '<p rend="bold">'
m = s.match(/rend="(.*?)"/)
p m[1] #=> "bold"
```

雖然 Ruby 支持任意數量的 captured groups，但能使用 backreference \n 語法的只支持 1-9。

\0 是個特殊的 backreference, 它指向整個 match 到的字串，也可以使用 MatchData[0]。  
注意 \0 這個 backreference 不能在 regexp 裡面使用，  
因為 backreferences 必須在 capture group 結束後才能使用，而 \0 backreference 指向的是「整個 match」這個隱含的 capture group.  
不過，這個 backreference 可以用於取代：  

```ruby
"The cat sat in the hat".gsub(/[csh]at/, '\0s')
# => "The cats sats in the hats"
```

### Named captures

https://ruby-doc.org/core-3.1.2/Regexp.html#class-Regexp-label-Named+captures 

使用 `(?<name>)` 或 `(?'name')` 捕捉的 Capture groups 也可以使用名稱來指向。

```ruby
/\$(?<dollars>\d+)\.(?<cents>\d+)/.match("$3.67")
   #=> #<MatchData "$3.67" dollars:"3" cents:"67">
/\$(?<dollars>\d+)\.(?<cents>\d+)/.match("$3.67")[:dollars] #=> "3"
```

一個 regexp 不能同時使用 named backreferences 以及 numbered backreferences。  
如果在 regexp 裡面使用了 named capture，那麼其他用小括號包起來的 group 就不會被捕捉。

```ruby
/(\w)(\w)/.match("ab").captures # => ["a", "b"]
/(\w)(\w)/.match("ab").named_captures # => {}
 
/(?<c>\w)(\w)/.match("ab").captures # => ["a"]
/(?<c>\w)(\w)/.match("ab").named_captures # => {"c"=>"a"}
```

如果在 =~ 運算元左邊是個 literal regexp，而且裡面使用了 named capture groups，  
捕捉到的文字也會 assign 到 區域變數。

```ruby
/\$(?<dollars>\d+)\.(?<cents>\d+)/ =~ "$3.67" #=> 0
dollars #=> "3"
```

## Grouping

https://ruby-doc.org/core-3.1.2/Regexp.html#class-Regexp-label-Grouping 

小括號包起來的文字，形成一個 group，允許對這個整體指定重複次數。

例如下面這個 pattern 是 match 一個母音後面跟著兩個字元：

```ruby
/[aeiou]\w{2}/.match("Caenorhabditis elegans") #=> #<MatchData "aen">
```

而底下的 pattern 則是「一個母音後面跟著一個字元」重複兩次，也就是 `[aeiou]\w[aeiou]\w`，例如 ‘enor’

```ruby
/([aeiou]\w){2}/.match("Caenorhabditis elegans")
   #=> #<MatchData "enor" 1:"or">
```

`(?:…)` 提供 grouping 但是不做 capturing.  
也就是說，括號內的文字會視為一個整體，但是不會產生 backreference.  
這犧牲一點可讀性，但是有助於提升效能。

## Alternation

https://ruby-doc.org/core-3.1.2/Regexp.html#class-Regexp-label-Alternation

`(a|b)` 表示 a 或 b

垂直線 (|) 將多個 expressions 結合為一個，符合其中任何一個都算 match。  
也是就說，每個 expression 都是個 alternative.

```ruby
/\w(and|or)\w/.match("Feliformia") #=> #<MatchData "form" 1:"or">
/\w(and|or)\w/.match("furandi")    #=> #<MatchData "randi" 1:"and">
/\w(and|or)\w/.match("dissemblance") #=> nil
```

## Anchors

https://ruby-doc.org/core-3.1.2/Regexp.html#class-Regexp-label-Anchors 

Anchors 是用來指定特定位置的 metacharacter.

| anchor | 說明 |
| ------ | ---- |
| ^      | 行首 |
| $      | 行尾 |
| \A     | 字串開頭 |
| \Z     | 字串結尾。如果字串結尾是 newline, 它會 match 在 newline 之前。 |
| \z     | 字串結尾 |
| \b     | 在中括號外面表示 word boundaries，在中括號裡面表示 backspace (0x08) |
| (?=pat) | Positive lookahead<br />確認後面的字元要 match pat, 但是不會把這些字元含入 matched text。 |
| (?!pat) | Negative lookahead<br />確認後面的字不能 match pat, 而且不會把這些字元含入 matched text。 |
| (?<=pat) | Positive lookbehind<br />確認前面的字元要 match pat, 但是不會把這些字元含入 matched text。 |
| (?<!pat) | Negative lookbehind<br />確認前面的字不能 match pat, 而且不會把這些字元含入 matched text。 |

如果 pattern 沒有指定 anchor, 那麼它可以從字串的任意位置開始：
```ruby
/real/.match("surrealist") #=> #<MatchData "real">
```

如果 anchor 在字串的開頭，那麼 pattern 就要從那裡開始 match.  
'real' 不在字串開頭，所以下面的 match 就失敗了：
```ruby
/\Areal/.match("surrealist") #=> nil
```

下面這個 match 也是失敗的，因為雖然 'Demand' 有包含 'and', 但是這個 pattern 並不是出現在 word boundary。
```ruby
/\band/.match("Demand")
```

下面的 pattern 使用 positive lookahead 以及 positive lookbehind 來 match 出現在標記內的文字，  
而不將標記包含到 match 裡：
```ruby
/(?<=<b>)\w+(?=<\/b>)/.match("Fortune favours the <b>bold</b>")
    #=> #<MatchData "bold">
```

## Options

https://ruby-doc.org/core-3.1.2/Regexp.html#class-Regexp-label-Options 

regexp 的 end delimiter（結束斜線）之後可以跟著一個或多個選項，用來控制這個 pattern 的 match 方式：

| option | 說明 |
| ------ | --- |
| /pat/i | 忽略大小寫 (Ignore case) |
| /pat/m | Multiline mode (多行模式)，點「.」也會 match 到 newline 字元 |
| /pat/x | eXtend，忽略 pattern 裡的空白和註解。 |

i, m, 跟 x 也可以在 subexpression 層級使用 (?on-off) 建構語法，  
這可以針對小括號裡的 expression 啟動某個選項或取消某個選項：
```ruby
/a(?i:b)c/.match('aBc')   #=> #<MatchData "aBc">
/a(?-i:b)c/i.match('ABC') #=> nil
```

也可以切換 pattern 某個位置之後的 options:
```ruby
/a(?i)bc/.match('abC') #=> #<MatchData "abC">
```

Regexp.new 也可以使用 options:
```ruby
Regexp.new("abc", Regexp::IGNORECASE)                     #=> /abc/i
Regexp.new("abc", Regexp::MULTILINE)                      #=> /abc/m
Regexp.new("abc # Comment", Regexp::EXTENDED)             #=> /abc # Comment/x
Regexp.new("abc", Regexp::IGNORECASE | Regexp::MULTILINE) #=> /abc/mi
```

## Free-Spacing Mode and Comments

https://ruby-doc.org/core-3.1.2/Regexp.html#class-Regexp-label-Free-Spacing+Mode+and+Comments 

選項 x 會啟動 free-spacing mode.  
pattern 裡的空格會被忽略，井號 (#) 表示註解開始，一直到行末。  
這可以將 pattern 組織為多個部件，增進可讀性。

例如這一段處理「連續悉曇字」的程式，將兩個以上的◇改為【◇】，regexp 太長太難閱讀：
```ruby
s.gsub!(/((（)|(［)|(？)|(…)|(．)|(‧))*◇((◇)|( )|(　)|(．)|(‧)|(（)|(）)|(［)|(］)|(？)|(…))*◇((）)|(］)|(？)|(…)|(．)|(‧))*/, '【◇】')
```

使用 free-spacing mode 就好讀多了，還可以對各個部件做註解：
```ruby
regexp = /
 [（［？…．‧]* # 在悉曇字前面也要包含進來的字元
 ◇
 [◇ 　．‧（）［］？…]* # 允許在悉曇字中間出現的字元
 ◇
 [）］？…．‧]* # 在悉曇字後面也要包含進來的字元
/x
s.gsub!(regexp, '【◇】')
```

因為空格會被忽略，如果要 match 空格，有幾種方法：
* 使用 `\s` 或 `\p{Space}`
* 使用 escaped whitespace 像這樣「`\ `」，就是在空白前加個 反斜線。
* 使用 character class 例如 `[ ]`

非 x 的 pattern, 也可以使用 `(?#comment)` 來寫註解。

## Special global variables

https://ruby-doc.org/core-3.1.2/Regexp.html#class-Regexp-label-Special+global+variables 

執行 Pattern match 時會設定一些 global variables :

|    | 說明 |
| -- | --- |
| $~ | 等同於 Regexp.last_match, 也就是最後一次 match 回傳的 matchdata 物件 |
| $& | 整個 match 到的文字 |
| $\` | 在 match 部分前面的字串 |
| $' | 在 match 部分後面的字串 |
| $1, $2….等 | capture group 裡的文字 |
| $+ | 最後一個 capture group |

範例
```ruby
m = /s(\w{2}).*(c)/.match('haystack') #=> #<MatchData "stac" 1:"ta" 2:"c">
$~                                    #=> #<MatchData "stac" 1:"ta" 2:"c">
Regexp.last_match                     #=> #<MatchData "stac" 1:"ta" 2:"c">
 
$&     #=> "stac", 跟 m[0] 一樣
$`     #=> "hay",  跟 m.pre_match 一樣
$'     #=> "k",    跟 m.post_match 一樣
$1     #=> "ta",   跟 m[1] 一樣
$2     #=> "c",    跟 m[2] 一樣
$3     #=> nil,    pattern 裡沒有第三個 group
$+     #=> "c",    跟 m[-1] 一樣
```

這些 global variables 是 thread-local 也是 method-local 變數。  
也就是說，在 thread, local 裡執行的 match, 不會影到外面。

例如：
```ruby
def match_another_string
 /(.*)基金會/.match('法鼓山文教基金會')
 p $1 # "法鼓山文教"
end
 
/(.*)基金會/.match('CBETA基金會')
p $1 # "CBETA"
match_another_string
p $1 # 還是 "CBETA"，沒有被改變
```
