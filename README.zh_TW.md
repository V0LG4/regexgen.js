# RegexGen.js - JavaScript Regular Expression 產生器

RegexGen.js 是開發給 JavaScript 使用的正則表達式產生器，可以使用淺顯易懂的語法來表現複雜的正則表達式。RegexGen.js 的開發是受到 [JSVerbalExpressions](https://github.com/VerbalExpressions/JSVerbalExpressions) 的啟發。

[![MIT](http://img.shields.io/badge/license-MIT-brightgreen.svg)](https://github.com/amobiz/regexgen.js/blob/master/LICENSE) [![npm version](https://badge.fury.io/js/regexgen.js.svg)](http://badge.fury.io/js/regexgen.js) [![David Dependency Badge](https://david-dm.org/amobiz/regexgen.js.svg)](https://david-dm.org/amobiz/regexgen.js)
[![Build Status](https://travis-ci.org/amobiz/regexgen.js.svg?branch=master)](https://travis-ci.org/amobiz/regexgen.js)

[![NPM](https://nodei.co/npm/regexgen.js.png?downloads=true&downloadRank=true&stars=true)](https://nodei.co/npm/regexgen.js.png?downloads=true&downloadRank=true&stars=true) [![NPM](https://nodei.co/npm-dl/regexgen.js.png?months=6&height=3)](https://nodei.co/npm/regexgen.js/)


RegexGen.js 基本上是為那些已經了解正則表達式引擎運作原理，但是不常使用正則表達式的人而開發的。可以這麼說，如果你切確知道存在某個表達式可以達成你的任務，但是卻經常需要查表才能寫出正確的表達式，那麼 RegexGen.js 也許就可以幫到你。即使是正則表達式的初學者，也能夠從 RegexGen.js 相對容易理解的表現方式，而快速地上手並使用簡單的正則表達式。

簡單地說，RegexGen.js 幫助人們：

1. 以容易分解以及容易理解的方式表現正則表達式。
2. 不必記憶正則表達式的『元字元 (meta-characters)』、『簡寫符號 (shortcuts)』，哪些字元在哪些情況下必須『跳脫 (escape)』，哪些情況下不需要？以及一些特殊的『極端情況 (corner cases)』，如 [What literal characters should be escaped in a regex?](http://stackoverflow.com/questions/5484084/what-literal-characters-should-be-escaped-in-a-regex/5484178#5484178)。
3. 重複使用正則表達式 (參考後面的『匹配 IP 位址』範例)。

## 解決的問題

RegexGen.js 努力減輕以下兩個問題：

1. 在撰寫正則表達式的時候，難以記憶正確的語法，以及需要『跳脫』的字元等。
2. 在正則表達式撰寫完成後，難以閱讀甚至無法理解其行為、運作原理。

## 目標

RegexGen.js 的設計，謹守著下列目標：

1. 寫出來的程式碼，應該易讀易懂。
2. 產出來的程式碼，應該要像人工寫的一樣緊湊，不要為了產生器本身容易撰寫，而產出機械式程式碼。尤其是不要產出不必要的 `{}` 或 `()`。
3. 不再需要手動對元字元進行轉義。 (除了 `\` 元字元本身。或者使用了表達式置換 (regex overwrite) 功能。)
4. 如果產生器力有未逮，無法產生理想的子表達式，必須要能夠在語法中直接指定替代的子表達式。也就是表達式置換功能。

## 開始使用

產生器是以 `regexGen()` 函數的形式提供。

要輸出正則表達式時，需要呼叫 `regexGen()` 函數，並將子表達式以參數的形式傳入。

個別的子表達式之間，像一般參數一樣，以 `,` 逗號隔開。最終輸出的正則表達式，將是這些子表達式組合而成的結果。

子表達式可以是字串、數字、一個標準的 RegExp 物件，或透過 `regexGen()` 函數提供的子方法 (也就是子產生器)，隨意加以組合 (參考後面的『[非正規 BNF 語法](#non-formal-bnf)』。

將字串傳入 `regexGen()` 函數，或 `text()`, `maybe()`, `anyCharOf()` 及 `anyCharBut()` 等子方法時，將自動視需要進行『跳脫』處理，因此你不需要記憶哪些字元需要在何時進行跳脫處裡。

最後，`regexGen()` 函數回傳一個 `RegExp` 物件，你接著可以像平常一樣使用該正則表達式物件。

使用時，大致上像這樣：

```
var regexGen = require('regexgen.js');
var regex = regexGen( sub-expression [, sub-expression ...] [, modifier ...] )
```

## <a id="non-formal-bnf"></a>非正規 BNF 語法

基本的使用方始，可以參考這裡列出的『非正規 BNF 語法』：

(注意，因為這裡列出的並不是嚴謹的[BNF 語法](http://en.wikipedia.org/wiki/Backus%E2%80%93Naur_Form)，故這裡稱為『非正規 BNF 語法』。)

```
regex ::= regexGen( sub-expression [, sub-expression ...] [, modifier ...] )

sub-expression ::= string | number | RegExp object | term

term ::= sub-generator() [.term-quantifier()] [.term-lookahead()]

sub-generator() ::= regexGen.startOfLine() | regexGen.endOfLine()
    | regexGen.wordBoundary() | regexGen.nonWordBoundary()
    | regexGen.text() | regexGen.maybe() | regexGen.anyChar() | regexGen.anyCharOf() | regexGen.anyCharBut()
    | regexGen.either() | regexGen.group() | regexGen.capture() | regexGen.sameAs()
    | regex() | ... (checkout wiki for all sub-generators.)

term-quantifier() ::= .term-quantifier-generator() [.term-quantifier-modifier()]

term-quantifier-generator() ::= term.any() | term.many() | term.maybe() | term.repeat() | term.multiple()

term-quantifier-modifier() ::= term.greedy() | term.lazy() | term.reluctant()

term-lookahead() ::= term.contains() | term.notContains() | term.followedBy() | term.notFollowedBy()

modifier ::= regexGen.ignoreCase() | regexGen.searchAll() | regexGen.searchMultiLine()
```

詳細的語法請參考 [wiki](wiki) 以及以下的說明及範例。更多的範例可以直接參考測試程式：[test.js](test.js)。

## 安裝

``` bash
npm install regexgen.js
```

## 用法

由於產生器是以 `regexGen()` 函數的形式提供，所有的子方法都是透過它來使用。建議可以先將它指定給比較簡短的變數，譬如 `_` 符號：

``` javascript
var _ = require('regexgen.js');
var regex = _(
    _.startOfLine(),
    _.capture( 'http', _.maybe( 's' ) ), '://',
    _.capture( _.anyCharBut( ':/' ).repeat() ),
    _.group( ':', _.capture( _.digital().multiple(2,4) ) ).maybe(), '/',
    _.capture( _.anything() ),
    _.endOfLine()
);
var matches = regex.exec( url );
```

注意：雖然不推薦，但是如果你認為以上的作法仍然不夠方便，同時也不介意污染全域物件 (譬如撰寫小型工具程式時)，你可以使用 `regexGen.mixin()` 函數，將 `regexGen()` 函數的所有的子方法，全部都匯出到全域物件中，這樣就可以省略上面範例中 '_.' 的部份：

``` javascript
var regexGen = require('regexgen.js');
regexGen.mixin( global );

var regex = regexGen(
    startOfLine(),
    capture( 'http', maybe( 's' ) ), '://',
    capture( anyCharBut( ':/' ).repeat() ),
    group( ':', capture( digital().multiple(2,4) ) ).maybe(), '/',
    capture( anything() ),
    endOfLine()
);
var matches = regex.exec( url );
```

## 關於回傳的 RegExp 物件

由 `regexGen()` 函數回傳的是一個標準的 `RegExp` 物件，你可以像平常一樣使用它。

不過為了提供除錯功能，以及在處理字串時，方便提取內容，該 `RegExp` 物件另外附加了四個屬性：
`warnings` 陣列, `captures` 陣列, `extract()` 方法以及 `replace()` 方法。
更多細節請參考 [wiki](wiki) 的說明。

## 範例

#### 簡單的密碼驗證

檢查一個字串，該字串必須至少包含六個字元，最多十個字元，字串必須混和數字、小寫、大寫英文字母，缺一不可。

這個範例取材自 [Mastering Lookahead and Lookbehind](http://www.rexegg.com/regex-lookarounds.html) 這篇文章。

``` javascript
var _ = require('regexgen.js');
var regex = _(
    // 錨點: 匹配字串的起始位置
    _.startOfLine(),
    // 匹配六到十個英數字字元
    _.word().multiple(6,10).
        // 順序環視：任意字元，直到發現一個小寫英文字母 (忽略優先)
        .contains( _.anything().reluctant(), _.anyCharOf(['a','z']) ).
        // 順序環視：任意字元，直到發現一個大寫英文字母 (忽略優先)
        .contains( _.anything().reluctant(), _.anyCharOf(['A','Z']) ).
        // 順序環視：任意字元，直到發現一個數字字元 (忽略優先)
        .contains( _.anything().reluctant(), _.digital() ),
    // 錨點: 匹配字串的結束位置
    _.endOfLine()
);
```
輸出為：
``` javascript
/^(?=.*?[a-z])(?=.*?[A-Z])(?=.*?\d)\w{6,10}$/
```

#### 匹配 IP 位址

這個範例取材自 [Mastering Regular Expressions](http://books.google.com.tw/books?id=sshKXlr32-AC&pg=PA187&lpg=PA187&dq=mastering+regular+expression+Matching+an+IP+Address&source=bl&ots=daK_ZPacNh&sig=l9eFfP2WvXWkTw_jYPQHSrxEO4Q&hl=zh-TW&sa=X&ei=z3KxU5blK43KkwXdiIGQDQ&ved=0CDcQ6AEwAg#v=onepage&q=mastering%20regular%20expression%20Matching%20an%20IP%20Address&f=false) 這本書。

``` javascript
var _ = require('regexgen.js');
// 0 ~ 199 的情形
var d1 = _.group( _.anyCharOf( '0', '1' ).maybe(), _.digital(), _.digital().maybe() );
// 200 ~ 249 的情形
var d2 = _.group( '2', _.anyCharOf( ['0', '4'] ), _.digital() );
// 250 ~ 255 的情形
var d3 = _.group( '25', _.anyCharOf( ['0', '5'] ) );
// 綜和上面三點，批配 0 ~ 255 的情形
var d255 = _.capture( _.either( d1, d2, d3 ) );
var regex = _(
    _.startOfLine(),
    d255, '.', d255, '.', d255, '.', d255,
    _.endOfLine()
);
```
輸出為：
``` javascript
/^([01]?\d\d?|2[0-4]\d|25[0-5])\.([01]?\d\d?|2[0-4]\d|25[0-5])\.([01]?\d\d?|2[0-4]\d|25[0-5])\.([01]?\d\d?|2[0-4]\d|25[0-5])$/
```

#### 匹配對稱的括號

這個範例取材自 [Mastering Regular Expressions](http://books.google.com.tw/books?id=sshKXlr32-AC&pg=PA193&lpg=PA193&dq=mastering+regular+expression+Matching+Balanced+Sets+of+Parentheses&source=bl&ots=daK_ZPaeHl&sig=gBcTaTIWQh-9_HSuINjQYHpFn7E&hl=zh-TW&sa=X&ei=YHOxU5_WCIzvkgX-nYHQAw&ved=0CBsQ6AEwAA#v=onepage&q=mastering%20regular%20expression%20Matching%20Balanced%20Sets%20of%20Parentheses&f=false) 這本書。

``` javascript
var _ = require('regexgen.js');
var regex = _(
    '(',
    _.anyCharBut( '()' ).any(),
    _.group(
        '(',
        _.anyCharBut( '()' ).any(),
        ')',
        _.anyCharBut( '()' ).any()
    ).any(),
    ')'
);
```
輸出為：
``` javascript
/\([^()]*(?:\([^()]*\)[^()]*)*\)/
```

#### 匹配任意巢狀深度的對稱括號

這個範例取材自 [Mastering Regular Expressions](http://books.google.com.tw/books?id=sshKXlr32-AC&pg=PA193&lpg=PA193&dq=mastering+regular+expression+Matching+Balanced+Sets+of+Parentheses&source=bl&ots=daK_ZPaeHl&sig=gBcTaTIWQh-9_HSuINjQYHpFn7E&hl=zh-TW&sa=X&ei=YHOxU5_WCIzvkgX-nYHQAw&ved=0CBsQ6AEwAA#v=onepage&q=mastering%20regular%20expression%20Matching%20Balanced%20Sets%20of%20Parentheses&f=false) 這本書。
``` javascript
var _ = require('regexgen.js');
function nestingParentheses( level ) {
    if ( level < 0 ) {
        return '';
    }
    if ( level === 0 ) {
        return _.anyCharBut( '()' ).any();
    }
    return _.either(
            _.anyCharBut( '()' ),
            _.group(
                '(',
                nestingParentheses( level - 1 ),
                ')'
            )
        ).any();
}
```
一層巢狀深度：
``` javascript
var regex = _(
    '(', nestingParentheses( 1 ), ')'
);
```
輸出為：
``` javascript
/\((?:[^()]|\([^()]*\))*\)/
```
三層巢狀深度：
``` javascript
var regex = _(
    '(', nestingParentheses( 3 ), ')'
);
```
輸出為：
``` javascript
/\((?:[^()]|\((?:[^()]|\((?:[^()]|\([^()]*\))*\))*\))*\)/
```


#### 匹配 HTML 標籤

這個範例取材自 [Mastering Regular Expressions](http://books.google.com.tw/books?id=GX3w_18-JegC&pg=PA200&lpg=PA200&dq=mastering+regular+expression+Matching+an+HTML+Tag&source=bl&ots=PJkiMpkrNX&sig=BiKB6kD_1ZudZw9g-VY-X-E-ylg&hl=zh-TW&sa=X&ei=y3OxU_uEIoPPkwXL3IHQCg&ved=0CFcQ6AEwBg#v=onepage&q=mastering%20regular%20expression%20Matching%20an%20HTML%20Tag&f=false) 這本書。
``` javascript
var _ = require('regexgen.js');
var regex = _(
    '<',
    _.either(
        _.group( '"', _.anyCharBut('"').any(), '"' ),
        _.group( "'", _.anyCharBut("'").any(), "'" ),
        _.group( _.anyCharBut( '"', "'", '>' ) )
    ).any(),
    '>'
);
```
輸出為：
``` javascript
/<(?:"[^"]*"|'[^']*'|[^"'>])*>/
```

#### 匹配 HTML anchor 連結

這個範例取材自 [Mastering Regular Expressions](http://books.google.com.tw/books?id=GX3w_18-JegC&pg=PA201&dq=mastering+regular+expression+Matching+an+HTML+Link&hl=zh-TW&sa=X&ei=QnSxU4W-CMLkkAWLjIDgCg&ved=0CBwQ6AEwAA#v=onepage&q=mastering%20regular%20expression%20Matching%20an%20HTML%20Link&f=false) 這本書。
``` javascript
var _ = require('regexgen.js');
var regexLink = _(
    '<a',
    _.wordBoundary(),
    _.capture(
        _.anyCharBut( '>' ).many()
    ),
    '>',
    _.capture(
        _.label( 'Link' ),
        _.anything().lazy()
    ),
    '</a>',
    _.ignoreCase(),
    _.searchAll()
);
var regexUrl = _(
    _.wordBoundary(),
    'href',
    _.space().any(), '=', _.space().any(),
    _.either(
        _.group( '"', _.capture( _.anyCharBut( '"' ).any() ), '"' ),
        _.group( "'", _.capture( _.anyCharBut( "'" ).any() ), "'" ),
        _.capture( _.anyCharBut( "'", '"', '>', _.space() ).many() )
    ),
    _.ignoreCase()
);
```
輸出為：
``` javascript
/<a\b([^>]+)>(.*?)<\/a>/gi
/\bhref\s*=\s*(?:"([^"]*)"|'([^']*)'|([^'">\s]+))/i
```
在瀏覽器中要遍歷所有的連結，可以這麼做：
```
var capture, guts, link, url, html = document.documentElement.outerHTML;
while ( (capture = regexLink.exec( html )) ) {
    guts = capture[ 1 ];
    link = capture[ 2 ];
    if ( (capture = regexUrl.exec( guts )) ) {
        url = capture[ 1 ] || capture[ 2 ] || capture[ 3 ];
    }
    console.log( url + ' with link text: ' + link );
}
```

#### 檢驗 HTTP URL

這個範例取材自 [Mastering Regular Expressions](http://books.google.com.tw/books?id=GX3w_18-JegC&pg=PA203&dq=mastering+regular+expression+Examining+an+HTTP+URL&hl=zh-TW&sa=X&ei=b3SxU9nUNojOkwXpjIDYCA&ved=0CBwQ6AEwAA#v=onepage&q=mastering%20regular%20expression%20Examining%20an%20HTTP%20URL&f=false) 這本書。
``` javascript
var _ = require('regexgen.js');
var regex = _(
    _.startOfLine(),
    'http', _.maybe( 's' ), '://',
    _.capture( _.anyCharBut( '/:' ).many() ),
    _.group( ':', _.capture( _.digital().many() ) ).maybe(),
    _.capture( '/', _.anything() ).maybe(),
    _.endOfLine()
);
```
輸出為：
``` javascript
/^https?:\/\/([^/:]+)(?::(\d+))?(\/.*)?$/
```
在瀏覽器中要印出匹配的 URL，可以這麼做：
``` javascript
var capture = location.href.match( regex );
var host = capture[1];
var port = capture[2] || 80;
var path = capture[3] || '/';
console.log( 'host:' + host + ', port:' + port + ', path:' + path );
```

#### 驗證 host 名稱

這個範例取材自 [Mastering Regular Expressions](http://books.google.com.tw/books?id=GX3w_18-JegC&pg=PA203&dq=mastering+regular+expression+Validating+a+Hostname&hl=zh-TW&sa=X&ei=hXSxU5nlKceIkQXc7YHgCA&ved=0CBwQ6AEwAA#v=onepage&q=mastering%20regular%20expression%20Validating%20a%20Hostname&f=false) 這本書。
``` javascript
var _ = require('regexgen.js');
var regex = _(
    _.startOfLine(),
    // 以 . 號分隔的部份 . . .
    _.either(
        _.group(
            _.anyCharOf( ['a', 'z'], ['0', '9'] ),
            '.'
        ),
        _.group(
            _.anyCharOf( ['a', 'z'], ['0', '9'] ),
            _.anyCharOf( '-', ['a', 'z'], ['0', '9'] ).multiple( 0, 61 ),
            _.anyCharOf( ['a', 'z'], ['0', '9'] ),
            '.'
        )
    ).any(),
    // 緊接著允許的域名分類 . . .
    _.either(
        'com', 'edu', 'gov', 'int', 'mil', 'net', 'org', 'biz', 'info', 'name', 'museum', 'coop', 'aero',
        _.group( _.anyCharOf( ['a', 'z'] ), _.anyCharOf( ['a', 'z'] ) )
    ),
    _.endOfLine()
);
```
輸出為：
``` javascript
/^(?:[a-z0-9]\.|[a-z0-9][-a-z0-9]{0,61}[a-z0-9]\.)*(?:com|edu|gov|int|mil|net|org|biz|info|name|museum|coop|aero|[a-z][a-z])$/
```

#### 解析 CSV 檔案

這個範例取材自 [Mastering Regular Expressions](http://books.google.com.tw/books?id=GX3w_18-JegC&pg=PA271&dq=Unrolling+the+CSV+regex&hl=zh-TW&sa=X&ei=x_q0U-qhD43jkAWYqoCgBA&ved=0CBwQ6AEwAA#v=onepage&q=Unrolling%20the%20CSV%20regex&f=false) 這本書。
``` javascript
var _ = require('regexgen.js');
var regex = _(
    _.either( _.startOfLine(), ',' ),
    _.either(
        // 要嘛是雙引號所包圍的內容
        _.group(
            // 起始雙引號
            '"',
            _.capture(
                _.anyCharBut( '"' ).any(),
                _.group(
                    '""',
                    _.anyCharBut( '"' ).any()
                ).any()
            ),
            // 結尾雙引號
            '"'
        ),
        // 不然就是除了雙引號、逗號之外的文字
        _.capture(
            _.anyCharBut( '",' ).any()
        )
    )
);
```
輸出為：
``` javascript
/(?:^|,)(?:"([^"]*(?:""[^"]*)*)"|([^",]*))/
```

## 測試

``` bash
$ npm test
```

## 修改記錄

[CHANGELOG](CHANGELOG.md)

## 作者

  * [Amobiz](https://github.com/amobiz)
