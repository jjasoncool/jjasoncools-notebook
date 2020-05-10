----------------------------------------
            一般語法參考
----------------------------------------
- cli (command line 部分)
  - -v 查看目前版本
  - --ini 可以查看目前php 使用哪個設定檔
  - -c (ini路徑) 可以指定 php 使用哪個地方的設定檔
    - 多版本PHP暫時找到的替代執行方式，linux可以透過 configure 指定 php.ini 位置
    - 但主要的 path 設定還是需要找到共存方法
    - `D:\xampp\php-7.3.14\php -c "D:\xampp\php-7.3.14\php.ini" D:\xampp\php-7.3.14\composer.phar`

- 實用常數
  - PHP_EOL 依照平台不同使用不同的 End Of Line symbol
  - PHP_OS 回傳作業系統類型，可以拿來判斷 linux 或是 windows
  - $_SERVER['HTTPS'] 可以拿來判斷伺服器是否有開啟 https

- 整齊的寫法
    >   運算子前後一定要有空白
    >   PHP tag 一定單獨佔據一行，並且在Code的最左方
    >   PHP tag 若在整個都是PHP的文件內，必須省略
```php
    <?php
        blah
    ?>
```
- 命名規則
    >   駝峰式寫法(camelCase CamelCase)
    >   Class ClassName
    >   function functionName
    >   parameter parameterName

- 編碼轉換
    iconv ("BIG5","UTF-8",$string)
    >   iconv( 來源編碼 , 輸出編碼[//參數:可加可不加] , 字串 )
    >   //TRANSLIT會自動將不能直接轉換的字符變成一個或多個近似的字符，
    >   //IGNORE會忽略掉不能轉化的字符，而默認效果是從第一個非法字符截斷。

    mb_convert_encoding ( $string, "UTF-8", "BIG5" )
    >   mb_convert_encoding( 字串 , 輸出編碼 , 來源編碼 )
    >   這個編碼模式將會將各種無論是否非法字符強制轉換，但效能較差

    string urlencode ( string $str )
    >   將文字編碼轉換為URL編碼
    >   範例:%E9%9B%BB%E8%85%A6%E7%9B%B8%E9%97%9C%E8%A8%AD%E5%82%99%E7%94%B3%E8%AB%8B%E8%A1%A8(電腦相關設備申請表)
    >   **有問題在於空格會被轉換成 + 號，若需要完整的轉換請改用 rawurlencode()

    string rawurlencode (string $str)
    >   功能同上述，但不會有符號混淆問題，與javascript 交換JSON 資料時可以避免特殊符號導致錯誤的情形

    string urldecode ( string $str )
    >   將所有網址列內%開頭的文字解碼為可讀文字
    >   $_GET 與 $_REQUEST 已有解碼，若使用此函數在此兩個全域變數上，可能會發生無法預期的結果

    rawurldecode ( string $str ) : string
    >   將所有網址列內%開頭的文字解碼為可讀文字
    >   特殊符號部分如 + 號或者 ~ 將可以正常轉換

- 字串
  - 字串基本常識
    >   單引號(Single quotes) 內不可以放變數，會將所有東西直接輸出的字串
    >   雙引號(Double quotes) 內可以直接放變數，會解析帶有$符號的變數
    >   **※注意:使用雙引號的變數，可以使用{}大括弧包起來，這樣便可以不需要使用連結符號 . 即可連接變數變成字串**
    ```php
    <?php
    // Use double quotes to concat more than two strings instead of multiple '.' operators.
    // PHP is forced to re-concatenate with every '.' operator.
    "{$str1}{$str2}{$str3}"; // one concat = fast
    $str1. $str2. $str3;   // two concats = slow
    ?>
    ```

  - strpos ( $被查詢字串, $欲查詢字串 [, $從第幾個字開始找，預設=0]) : `int`
  - mb_strpos ( $被查詢字串, $欲查詢字串 [, $從第幾個字開始找，預設=0 [, $字串編碼]] ) : `int`
    >   注意，若字串存在於開頭，但strpos會回傳0，此值會被當作false
    >   故在判斷時必須使用 > 0(存在，但不在字串開頭) 或者 === false(不存在，即使在字串開頭) 以及 === 0(在字串開頭) 來避免一些錯誤
    >   回傳字串位置，否則回傳false(注意型別，可用===或!== 才是boolean型別判斷)

  - stripos ( string $haystack , mixed $needle [, int $offset = 0 ] ) : `int`
  - mb_stripos ( string $haystack , string $needle [, int $offset = 0 [, string $encoding = mb_internal_encoding() ]] ) : `int`
    >   主要差別為**strpos()**是有區分大小寫的，而**stripos()**不區分大小寫

  - substr ( $要裁切的字串 , $字串裁切起點(第一個為0，可為負值，負值由尾端反向裁切) [, $需裁切長度(負值為由起始字串切到由後往前算的字) ] ) : `string`
  - mb_substr ( $要裁切的字串 , $字串裁切起點(第一個為0，可為負值，負值由尾端反向裁切) [, $需裁切長度(負值為由起始字串切到由後往前算的字)[, $字串編碼 ]] ) : `string`
    >   裁切長度若被省略則直接由指定裁切起點裁切至字串結束點
    >   ※缺點：UTF8或者中文字有可能會被截斷，因UTF8為3個位元組，BIG5為兩個位元組
    >   基本上此函數雷同於substr，但是確保了字元的完整性

    (int) strcmp ($字串1, $字串2)
    >   回傳值如果為0則字串相等
    >   如果字串1比字串2短，回傳值小於0
    >   如果字串1比字串2長，回傳值大於0
    >   通常可以用於字串長度排序

    (int) strlen ($字串)
    >   回傳字串長度

    (int) preg_match ( "正規表示式尋找字串" , "被尋找字串" [, array &$matches [, int $flags = 0 [, int $offset = 0 ]]] )
    >   回傳匹配字串位置，使用正規表示式
    >

    ucfirst ( string $str ) : string
    >   將英文字母第一個字換成大寫，可以配合lowercase

    lcfirst ( string $str ) : string
    >   將英文字母第一個字換成小寫，可以配合uppercase

    (mixed) eval ( $string )
    >   回傳將字串作為PHP運算後得到之結果 例如：
    ```php
    $strCode = "1+1";
    // $Value 值會為2
    $Value = eval("return ".$strCode.";");
    ```

  - sprintf ($格式化字串,arg1,arg2,arg++) : `string`
    >   將透過%符號傳遞參數
    ```php
    $txt = sprintf("There are %u million bicycles in %s.",$number,$str);
    ```
    >   則將 $number 參數傳入 %u , $str 參數傳入 %s
    >   ※格式說明如下
    1. % - a literal percent character. No argument is required.
    2. b - the argument is treated as an integer and presented as a binary number.
    3. c - the argument is treated as an integer and presented as the character with that ASCII value.
    4. d - the argument is treated as an integer and presented as a (signed) decimal number.
    5. e - the argument is treated as scientific notation (e.g. 1.2e+2). The precision specifier stands for the number of digits after the decimal point since PHP 5.2.1. In earlier versions, it was taken as number of significant digits (one less).
    6. E - like %e but uses uppercase letter (e.g. 1.2E+2).
    7. f - the argument is treated as a float and presented as a floating-point number (locale aware).
    8. F - the argument is treated as a float and presented as a floating-point number (non-locale aware). Available since PHP 5.0.3.
    9. g - shorter of %e and %f.
    10. G - shorter of %E and %f.
    11. o - the argument is treated as an integer and presented as an octal number.
    12. s - the argument is treated as and presented as a string.
    13. u - the argument is treated as an integer and presented as an unsigned decimal number.
    14. x - the argument is treated as an integer and presented as a hexadecimal number (with lowercase letters).
    15. X - the argument is treated as an integer and presented as a hexadecimal number (with uppercase letters).

  - explode ( string delimiter:分割字串的符號 , string $被分割字串 [, int $limit = PHP_INT_MAX ] ) : `array`
    >   回傳陣列，陣列內的元素為字串被分割符號切開的分割字串
    >   如果 delimiter 為空字符串（""），explode() 將返回 FALSE
    >   limit 為正數，則回傳limit 數量的陣列；若為負數，則回傳總數量扣除limit數量的陣列，若為0則視為1
    >   注意:若被分割字串為空字串，explode 後會產生1個key為0的陣列元素，為避免此狀況，可利用 **array_filter()**

    (string/array) str_replace ( mixed $search , mixed $replace , mixed $subject [, int &$count ] )
    >   可使用陣列方式取代字串值， $search 將會鏡像對應 $replace 值，若 $replace 數量少於 $search ，其餘被取代字串將會被替代為空字符串
    >   使用陣列串值時，將會由第一個開始依序取代字元，例如 a 改成 apple，p 改成 pear，字串輸入 a p ，期望結果是 apple pear，但實際結果會是 apearpearle pear，可參考 **strtr()**
    >   $replace 若只有一個，則所有的 $search 陣列內元素將會被取代為 $replace 字串
    >   count若有傳入參數，將會被設值，該值為取代了多少結果

  - strtr ( string $str , string $from , string $to ) `string`
  - strtr ( string $str , array $replace_pairs ) `string`
    >   此函數功能為，依照字串對應更新，或者使用陣列，將對應key值置換為value值
    >   如果 **from 與 to 長度不相等**，那麼多餘的字符部分將被忽略。 str 的長度將會和返回的值一樣
    >   此方法不會重複更新，只會搜尋有對應的值做一次取代 (這是與 **str_replace()** 的不同點)

    (string) htmlentities ( string $string [, int $flags = ENT_COMPAT | ENT_HTML401 [, string $encoding = ini_get("default_charset") [, bool $double_encode = TRUE ]]] )
    (string) html_entity_decode ( string $string [, int $flags = ENT_COMPAT | ENT_HTML401 [, string $encoding = ini_get("default_charset") ]] )
    >   php 5.2 這邊有雷，會把utf8的東西全部轉成亂碼
    >   將html特殊字符轉換為對應的characters
    >   htmlentities 轉換所有含有對應“html實體”的特殊字元，比如貨幣表示符號歐元英鎊等、版權符號等
    >   對應編碼須符合正確，否則可能出現亂碼結果
    >   $flags 有幾種
        ENT_COMPAT	    會轉換雙引號，不轉換單引號。
        ENT_QUOTES	    既轉換雙引號也轉換單引號。
        ENT_NOQUOTES	單/雙引號都不轉換
        ENT_IGNORE	    丟棄無效的代碼單元序列，而不是返回空字串。 不建議使用此標記， 因為它» 可能有安全影響。
        ENT_SUBSTITUTE	替換無效的代碼單元序列為 Unicode 代替符（Replacement Character）， U+FFFD (UTF-8) 或者 &#xFFFD; (其他)，而不是返回空字串。
        ENT_DISALLOWED	為文件的無效代碼點替換為 Unicode 代替符（Replacement Character）： U+FFFD (UTF-8)，或 &#xFFFD;（其他），而不是把它們留在原處。 比如以下情況下就很有用：要保證 XML 文檔嵌入額外內容時格式合法。
        ENT_HTML401	    以 HTML 4.01 處理程式碼。
        ENT_XML1	    以 XML 1 處理程式碼。
        ENT_XHTML	    以 XHTML 處理程式碼。
        ENT_HTML5	    以 HTML 5 處理程式碼。

    htmlspecialchars ( string $string [, int $flags = ENT_COMPAT | ENT_HTML401 [, string $encoding = ini_get("default_charset") [, bool $double_encode = TRUE ]]] ) : string
    htmlspecialchars_decode ( string $已轉換字串 [, int $flags = ENT_COMPAT | ENT_HTML401 ] ) : string
    >   此方法與**htmlentities**差別在於連特殊字元都會轉換，htmlspecialchars 只轉換（& < > ' "）這幾個字元
    >   flag 若用最新HTML5 可以使用 ENT_HTML5，字符編碼依據php版本不同有不同的編碼，但主要用UTF-8 (同下表)
    >   $encoding 5.6版本以後預設為php.ini中的default_charset,5.5~5.4預設為UTF8,5.3以前預設為latin1
    >   $double_encode 如果為off將不轉換已存在的html entities，預設為轉換所有字元
    >   運用此函數在輸出入可以提高安全性以及確保輸出避免與程式碼混淆

    (string) strip_tags ( string $str [, string $allowable_tags ] )
    >   過濾掉html的TAG字串，僅保留其內容
    >   $allowable_tags 為可允許存在的html TAG，若有設定則該TAG不會被拿掉
    >   補充： 搜尋 strip_tags_content

    (string) addslashes ( string $str )
    >   會將以下字元做跳脫，另外在處理許功蓋BIG5的編碼問題塞入DB的時候也會利用到此函數
    >   single quote (')
    >   double quote (")
    >   backslash ( \ )

    (string) stripslashes ( string $str )
    >   將跳脫字元的字符還原成沒有跳脫的樣式
    >   與get_magic_quotes_gpc有關

    (string) nl2br ( string $string [, bool $is_xhtml = TRUE ] )
    >   幫助像是textarea的換行符 \n 轉換成為html <br>
    >   在5.3版本之後才有 $is_xhtml，若此值為true則返回<br />

    strrchr ( string $被擷取字串 , mixed $needle ) : string
    >   返回最後 needle 的字串作為開始處，直到字串末尾

- 特殊字串處理
    (array) imap_rfc822_parse_adrlist ( string $address , string $default_host )
    >   將一串電子郵件地址轉換為 **ArrayObject** (PHP5 之後才有ArrayObject)
    >   可參考**class.phpmailer**內使用來檢驗email位址是否正確

    特殊處理 XML/HTML **DOMDocument class**
    >   可以用來抓取html字串等等
    ```php
        $document = new DOMDocument();
        // 去除圖片
        $document->loadHTML(preg_replace("/<img[^>]+\>/i", "", $htmlString));
        // 藏有特殊ID
        $inputs = $document->getElementsByTagName("input");
        // 這邊其實只有一個 input
        foreach ($inputs as $input) {
            $value = $input->getAttribute("value");
        }
        unset($document);
    ```

    pathinfo ( string $path [, int $options = PATHINFO_DIRNAME | PATHINFO_BASENAME | PATHINFO_EXTENSION | PATHINFO_FILENAME ] ) : mixed
    >   處理檔名使用的
    >   pathinfo() 是地區編碼性的，若是混了了非支援的編碼，需要先做 `setlocale(LC_ALL, 'zh_TW.UTF-8');`


- 數值
    (int) intval (mixed $var)
    >   轉換參數為數值

    (bool) is_numeric ( mixed $var )
    >   判斷為純數字時，輸出true

    (string) number_format ( float $number , int $decimals = 0 , string $dec_point = "." , string $thousands_sep = "," )
    >   決定數字顯示格式，第一個參數為數字變數、第二個參數為小數點數量、第三個參數為小數點顯示符號(依據國情不同)、第四個參數為千分位符號(依據國情不同) 預設四捨五入

- 迴圈與跳出
    break
    >   中斷當前的 for, foreach, while, do-while, switch 結構式
    >   並且可以指令需要中斷多少個巢狀結構，預設值為1，意指中斷當前執行之上述類型結構式

```php
    <?php
    $i = 1;
    while (true) { // 看起來像無窮迴圈
        if ($i==2) {// 2跳過不顯示，continue 代表迴圈持續執行，只是跳過單輪而已
            $i++;
            continue;
        } else if ($i==5) {// 但到這邊 $i == 5 跳出迴圈，直接跳出，無論後面是否還有迴圈
            break;
        } else {
            echo $i . '<br>';
        }
        $i++;
    }
    exit;
    echo '這裡不輸出';
    ?>
```

- 陣列
    1. 陣列處理
        list( $要塞入的變數 ) = $array
        >   將陣列中的值塞入變數之中
        >   list 只對數字key型態的陣列生效，key從0開始
        >

        unset()
        >   重置、刪除變數
        >   但如果在區域function 內 unset 公用變數，則不影響公用變數值，僅在function內unset而已
        >   若要在function 內 unset 公用變數值，則需使用 unset($GLOBALS["blah"])

        sizeof()
        >   陣列大小、陣列長度
        >   同時為 count() 的別名

        $變數 = array();
        >   要做push之前一定要先做宣告
        >   若要避免notice 可使用 array_fill()函數

        array_fill ($開始的陣列index, $填滿幾個, $填入值)
        >   此函數會使用數字型態index 填滿指定範圍的陣列
        >   可利用來避免陣列notice，例：$element = !empty( $_POST["dataString"] ) ? explode(",", $_POST["dataString"]) : array_fill(0,5,"");

        (int) array_push ($要塞入的陣列, $值1, $值2, ...)
        >   將值塞入陣列內，但key 只會為數值，可與for迴圈合併使用，可一次塞入多項數值
        >   若並非要塞入多像數值，建議使用 $變數名稱[] = $值 ，效能較佳
        >   若要塞入的陣列沒有預先定義為array，則會跳出warning
        >   回傳值為成功新增的元素數量

        array_unshift ( array &$array [, mixed $... ] ) : int
        >   新增項目到陣列清單的開頭
        >   將元素放至陣列前方，但順序會依照，傳入值的順序整體塞入前方

        (array) array_keys ($陣列);
        >   回傳一個陣列的所有key值

        (array) array_values ($陣列);
        >   回傳一個陣列的所有value值

        array_column ( array $input , mixed $column_key [, mixed $index_key = NULL ] ) : array
        >   回傳二度陣列所有指定的相同 key 為新陣列
        >   $index_key 則是將，輸入陣列中的另外一個欄位作為新陣列的 key
        ```php
        $records = array(
            array(
                'id' => 2135,
                'first_name' => 'John',
                'last_name' => 'Doe',
            ),
            array(
                'id' => 3245,
                'first_name' => 'Sally',
                'last_name' => 'Smith',
            ),
        );
        array_column($records, 'last_name', 'id');
        // 得到以下結果
        array(2135 => 'Doe' , 3245 => 'Smith')
        ```

        (string) implode ( string $接合字串 , array $陣列 )
        >   回傳將陣列元素使用特定接合字串接合起來的長字串

        (array) array_filter ( array $傳入陣列 [, callable $被呼叫函數 [, int $flag = 0 ]] )
        >   將陣列的每一個value值傳遞到可以被呼叫的函數內，若函數回傳值為true，則該值將會被保留在回傳陣列之內，且該回傳陣列key值與原先傳入陣列相同
        >   若沒有被呼叫的函數，則將會過濾掉等效為false值之陣列，若想保留0值時，可利用strlen保留值為0的元素 `array_filter($array, 'strlen')`

        (array) array_diff ( array $主要陣列 , array $參考陣列 [, array $... ] )
        >   將主要陣列對照其他參考陣列，此函數回傳主要陣列中存在但不存在於其他參考陣列中的值
        >   也就是只有主要陣列中特殊值會被保留下來
        >   即使參考陣列中有其他主要陣列沒有的值，也不會被回傳

        (array) array_combine ( array $keys , array $values )
        >   合成兩個陣列，成為一個索引與值互相對應的陣列
        >   其中一個陣列當 key 值，另外的陣列當作陣列值

        (array) array_unique ( array $array [, int $sort_flags = SORT_STRING ] )
        >   將陣列中重複值去除，若陣列兩者比較為重複相同，前者會被保留
        >   sort_flags，第二個變數用來設定陣列值判斷比較條件
         - SORT_REGULAR - compare items normally (don't change types)
         - SORT_NUMERIC - compare items numerically
         - SORT_STRING - compare items as strings
         - SORT_LOCALE_STRING - compare items as strings, based on the current locale.

       - array_merge ([ array $... ] ) : `array`
        >   將陣列合併起來，非陣列元素也可以被合併
        >   要注意的是若是以數值作為基底的，將會持續附加到後面，並重新賦予新的連續索引值 key
        >   若是文字的索引，則最後相同索引值的 value 將覆蓋先前相同索引的 value
        >   注意: 如果要讓相同數字索引以後來的陣列去覆蓋合併的話，直接用 **+** 運算子即可

        (array) array_flip  ( array $array )
        >   陣列索引與值做交換

        (array) array_intersect_key ( array $array1 , array $array2 [, array $... ] )
        >   以第1個陣列為基礎，將陣列2以後存在的key當作回傳的陣列key，並以這些key來取得陣列1的值
        >   陣列2以後的值不使用
        >   用來取得某固定範圍的key值做使用

    1. 判斷類
        (boolean) boolval(混合型別 $要轉換的變數)
        >   將輸入參數轉為 boolean 值，最常見為0和1轉為false和true

        (boolean) empty()
        >   判斷陣列或值是否為空值或者是0，其他值則回傳FALSE

        (boolean) isset()
        >   判斷值是否存在，若為NULL或者不存在，則回傳FALSE，其他為TRUE

        (boolean) in_array ( 混合型別 $要找尋的變數 , array $於哪個陣列搜尋 [, bool $strict = FALSE ] )
        >   第三個變數預設為FALSE，不比較型別，但有人指出不使用strict 方式，將會出現部分錯誤
        >   此函數為比對在array 內的物件是否存在，並返回 boolean 值

        (boolean) array_key_exists ( mixed $key , array $被搜尋的陣列 )
        >   傳入值若沒有在陣列之中，則回傳false

        (mixed) array_search ( $要找尋的變數值, array $被搜尋的array [, bool $strict = false ] )
        >   搜尋陣列內是否有值，若有則回傳該陣列的key值，可配合array_column()搜尋二維陣列，或是使用foreach 迴圈的$value 放入array_search尋找

    2. 其他應用
        Variable functions
        >   字串可以直接當成function name 使用
        ```php
            $func = 'foo';
            $func();        // This calls foo()

            class Foo
            {
                static function bar()
                {
                    echo "bar\n";
                }
                function baz()
                {
                    echo "baz\n";
                }
            }

            $func = array("Foo", "bar");
            $func(); // prints "bar"
            $func = array(new Foo, "baz");
            $func(); // prints "baz"
            $func = "Foo::bar";
            $func(); // prints "bar" as of PHP 7.0.0; prior, it raised a fatal error
        ```

        call_user_func ( callable $callback [, mixed $parameter [, mixed $... ]] )
        call_user_func_array ( callable $callback , array $param_arr )
        >   這些方式都可以動態調用函數，但尤以 Variable functions 效能最好
        >   call_user_func_array 可用在動態產生變數傳入函數，在無法預期傳入變數的數量時可利用陣列傳入來進行運算
        最常見的例子為 call_user_func_array(array($stmt, 'bind_param'), $stmt_param);
        ※注意：PHP5.3版本以上，的參數需要passed by reference，否則將出現錯誤
        ```php
            foreach ($preStmtParam as $key => $value) {
                $passByRef[$key] = &$preStmtParam[$key];
            }
        ```
        >   $callback 的部分若非動態函數，則是用字串代表該函數

        (string) http_build_query ( mixed $query_data [, string $numeric_prefix [, string $arg_separator [, int $enc_type = PHP_QUERY_RFC1738 ]]] )
        >   將陣列或參數轉換為網址的GET參數
        >   $query_data 可以使用物件或陣列，物件僅有public參數會被轉換
        >   $numeric_prefix 若有設定，且傳入陣列沒有指定key時，將會自動產生設定的字串開頭連接數字key，例如設定obj_，將會產生obj_1=...&ogj_2=...&......
        >   $arg_separator 代表割開每個變數的字元，預設為"&"，定義此選項可更改分割字串
        >   $enc_type 預設為PHP_QUERY_RFC1738標準，此標準會把空格編碼成+號，但有可能與真正的+號混淆，另一選項為PHP_QUERY_RFC3986標準，空格將會被編碼成%20，不影響其他字符
        ```php
        // 可自訂只需要抓那些參數
        $findkey = array_flip(array('error', 'msg'));
        $param = http_build_query(array_intersect_key($_GET, $findkey));
        ```

        (string) file_get_contents  ( string $filename [, bool $use_include_path = FALSE [, resource $context [, int $offset = 0 [, int $maxlen ]]]] )
        >   抓取網址或者文本的方法
        >   若是要做為執行某PHP的方式，盡量還是用CURL，速度差10倍

    3. 動態陣列
        陣列內的值可以用 &$變數(指向同一記憶體位址) 但目前似乎不能串接字串，只能是單獨值，因此必須使用一個欄位來做承接
        >   array1(&$test)
        >   array2(&$test)
        >   function (array1 , array2) .... 修改 $test 值，兩個陣列的值會同步變更，至於 $test 是否需宣告，則看是否有將此參數傳進入function(&$test)
        >   若僅是針對 array[0] 對址修改，其實test要取甚麼名字都無所謂(也無須宣告)

        (array) array_map ( callable $callback , array $array1 [, array $... ] )
        >   可以在第一個參數放callable function (也就是有回傳值的函數)
        >   此函數將會將每一個 array 值，都透過 $callback 處理過一次，返還array
        >   若callback函數需要有兩個參數，則array1需對應callback的參數數量放置對應的array

    4. 資料編碼
        資料透過陣列轉換成json字串，或將json字串轉換成陣列
        json_encode( mixed $value [, int $options = 0 [, int $depth = 512 ]] );
        >   通常為將陣列轉為json字串，也可以使用物件
        >   PHP5.4 支援 JSON_PRETTY_PRINT
        >   PHP5.4之後，UTF-8的字串會被escape，若不要被escape，可用選項 JSON_UNESCAPED_UNICODE
        json_decode( string $json [, bool $assoc = FALSE [, int $depth = 512 [, int $options = 0 ]]] );
        >   $assoc 設為 true 時，轉換時的key將會是根據陣列的key值決定，否則為123

- 變數
    Using ${} is a way to create dynamic variables
    動態產生變數，可連結字串


- 路徑處理
    - pathinfo ( string $path [, int $options = PATHINFO_DIRNAME | PATHINFO_BASENAME | PATHINFO_EXTENSION | PATHINFO_FILENAME ] ) : `array/string`
    >   回傳路徑相關資料
    >   若沒有指定 option 回傳陣列
    >   filename 是5.2以後有的選項不須擔心，為避免健忘 **basename($filename, '.html');** 可以達到類似效果

    ```php
    // 一般 root_depth = 1，除非架構在子目錄之下
    function get_relative_path($file)
    {
        $folder_depth = substr_count($_SERVER["PHP_SELF"], "/");
        if ($folder_depth == false) {
            $folder_depth = 1;
        }

        $prefix = (($folder_depth - root_depth > 0) ? str_repeat("../", $folder_depth - root_depth) : "");
        $path = $prefix . $file;
        return $path;
    }
    ```
    -  realpath ( string $path ) : `string`

- 日期與時間處理
  - ※注意:2038 timestamp 會溢出32位元極限，因此要使用 timestamp 相關函數需要注意。
    (timestamp) strtotime()
    >   回傳時間戳
    (timestamp) mktime($時, $分, $秒, $月, $日, $年, $是否為日光節約時間 = -1);
    >   此函數可利用來計算時間區間，因使用時間戳記，故就算在函式內加減都沒有問題
    >   經測試 mktime($時, $分, $秒, $月=date("n"), $日=date("j"), $年=date("Y")) 時，取得現在時間可將值代0，但年月日請不要代0，須代入date函數，避免錯誤
    >   另外值內可以做加減 (不過如果要計算時間日期區間，建議使用MYSQL的function比較不會有問題)

    date_default_timezone_set ( string $timezone_identifier )
    >   在其中一支程式內套用選定時區，不影響其他程式碼
  - DateTime Object
    (object)
    ```php
    // 新增 datetime 物件 (php 5.2 可以用的日期物件)
    $now = new DateTime();
    $temp_time = new DateTime($row['temp_time']);
    $temp_time->modify('+3 day');
    echo $temp_time->format('Y-m-d H:i:s');
    echo $now < $temp_time;
    // datetime 常用的函數 (php 5.3以後)

    ```

- 加解密與壓縮處理

    (string) base64_encode ( string $data )
    (mix)    base64_decode ( string $data [, bool $strict = FALSE ] )
    >   可將二進位資料壓縮成僅用英文以及符號傳遞的加密字串，Base64可以用來將binary的位元組序列資料編碼成ASCII字元序列構成的文字
    >   使用的字元包括大小寫拉丁字母各26個、數位10個、加號+和斜槓/，共64個字元，等號=用來作為字尾用途

    (string) gzcompress ( string $data [, int $level = -1 [, int $encoding = ZLIB_ENCODING_DEFLATE ]] )
    (string) gzuncompress ( string $data [, int $length = 0 ] )
    >   壓縮字串的函式

- 緩衝區：主要用途為，當PHP仍然在執行，但有訊息想要先行輸出的時候，可以使用此緩衝區先傳送或處理訊息
  - ob_flush()
    >   將目前的輸出內容送至緩衝區
    1. flush() 無論PHP 目前執行在任何情況，將目前buffer內的內容輸出至瀏覽器

  - ob_implicit_flush( [ int $flag = 1 ] )
    >   開啟隱式 flush 模式。開啟模式後，不需要每次都呼叫 flush() 函數輸出內容
    >   1 to turn implicit flushing on, 0 otherwise.

  - ob_get_level ()
    >   For users confused about getting "1" as a return value from ob_get_level at the beginning of a script: this likely means the PHP ini directive "output_buffering" is not set to off / 0. PHP automatically starts output buffering for all your scripts if this directive is not off (which acts as if you called ob_start on the first line of your script).

  - ob_start( [ callable $output_callback = NULL [, int $chunk_size = 0 [, int $flags = PHP_OUTPUT_HANDLER_STDFLAGS ]]] )
    >   開啟緩衝區，期間輸出所有資料將不會被輸出
    >   callable 為放置可以呼叫的函數，當ob_start時， 會將內容經由callable函數處理一次，再放到緩衝區
    >   可以配合的函數為：
    1. ob_get_contents() 將返回ob buffer內的字串，若ob buffer沒有開啟則返回false
    2. ob_end_flush() 將buffer內容輸出，並關閉buffer
    3. ob_clean() 將會把緩衝區內容丟棄，但不會關閉緩衝區
    4. ob_end_clean() 將會把緩衝區內容丟棄並關閉
    5. ob_iconv_handler 將buffer內字符編碼從 internal_encoding 轉換到 output_encoding (需在php.ini內設定或函數定義)
        **注意:若有先定義header則會衝突** Cannot modify header information - headers already sent by (xxx.php)
        >   使用方式:
        ```php
        iconv_set_encoding("internal_encoding", "UTF-8");
        iconv_set_encoding("output_encoding", "ISO-8859-1");
        // start output buffering
        ob_start("ob_iconv_handler");
        ```

    6. 引用檔案轉編碼範例(實用)
        ```php
        // 範例 (檔案為BIG5編碼)，但輸出為UTF-8)
        // 這個有先輸出的時候不能用 (header衝突)
        iconv_set_encoding("internal_encoding", "BIG5");
        iconv_set_encoding("output_encoding", "UTF-8");
        // start output buffering
        ob_start("ob_iconv_handler");
        include_once '../cr/topMenu.php';
        // 輸出轉編碼也可以
        // $topMenu = ob_get_contents();
        ob_end_flush();

        // 範例2
        // start output buffering (無腦轉編碼)
        ob_start();
        require_once 'topMenu.php';
        $topMenu = ob_get_contents();
        ob_end_clean();

        $db->query('SET NAMES UTF8');
        echo iconv('BIG5', 'UTF-8', $topMenu);
        ```
----------------------------------------
            圖像處理
----------------------------------------


----------------------------------------
            檔案操作
----------------------------------------
- ini_get('upload_max_filesize')
    >   抓取php.ini 的資料，此處為抓取可上傳的檔案容量
    >   post_max_size 如果檔案上傳透過form post，則需要設定此值大於或等於upload_max_filesize

- file_put_contents ( string $filename , mixed $data [, int $flags = 0 [, resource $context ]] ) : *boolean*
    >   如果檔案不存在將會創建一個新檔案，存在將會覆寫，除非有 flag 使用 FILE_APPEND

- file_exists ( string $filename ) : *boolean*
- is_file ( string $filename ) : *boolean*
    >   可以判斷檔案是否存在，但會寫入catch，可用clearstatcache()清空快取資料
    >   PHP 的 is_file() 及 file_exists() 都是用作檢查檔案是否存在，它們的分別是 file_exists() 輸入的參數是目錄也會回傳 TRUE，而 is_file() 則只會對檔案回傳 TRUE

- unlink ( string $filename [, resource $context ] ) : *boolean*
    >   刪除檔案，刪除成功將會回傳 boolean 值

- rename ( string $oldname , string $newname [, resource $context ] ) : *boolean*
    >   重新命名檔案，成功與否回傳boolean

- is_dir ( string $filename ) : *boolean*
    >   確認該路徑為目錄
    >   Returns TRUE if the filename exists and is a directory, FALSE otherwise.


- scandir ( string $directory [, int $sorting_order = SCANDIR_SORT_ASCENDING [, resource $context ]] ) : `array`
    >   將目錄中的所有檔案列出，但會包含兩個目錄也被列出 ./ ../ ， 可利用array_diff或array_slice去除
    >   $sorting_order 可影響檔案讀出來的排列方式，基本上不會動 (以利套用array_slice)

- readdir ([ resource $dir_handle ] ) : `string`
    >   需使用經由 opendir() 方法的 resource

- move_uploaded_file ( string $filename , string $path ) : *boolean*
    >   將經由合法上傳的文件移動至指定路徑，成功時回傳TRUE
    >   合法定義:經由 HTTP POST 上傳
    >   可以利用 PHP_OS 判斷作業系統，若LINUX為UTF-8檔名機制，若網頁仍為BIG5，需轉換檔名
    >   ※注意：如果文件已存在將會被覆蓋

- readfile( string $filename )
    >   readfile() will not present any memory issues, even when sending large files, on its own.
    >   If you encounter an out of memory error ensure that output buffering is off with ob_get_level().
    >   若讀取大型檔案的時候會超過記憶體，則可以另外加上
    ```php
    // read big file will not error
    if (ob_get_level()) {
        ob_end_clean();
    }
    ```

- Class ZipArchive
    >   PHP 5.2 之後才會支援的 Class，主要用來做zip壓縮相關的操作
    - method:
        (ture/error) ZipArchive::open ( string $filename [, int $flags ] )
        >   開啟新的ZIP檔案或者取代原有檔案
        >   $flags 存在以下四種，可以使用 || && 合併使用
            ZipArchive::CREATE (integer) 如果ZIP檔案不存在則創建一個
            ZipArchive::OVERWRITE (integer) ZIP檔案存在，直接覆蓋掉
            ZipArchive::EXCL (integer) 如果存在檔案則回傳錯誤
            ZipArchive::CHECKCONS (integer) 對存檔執行其他一致性檢查，如果失敗則會出錯
        >   可以利用 $zip->open( $zipFileName, ZIPARCHIVE::CREATE | ZIPARCHIVE::OVERWRITE );
            進行對ZIP檔案無論存在與否，都進行蓋檔案動作

        (boolean) ZipArchive::addFile ( string $filename [, string $localname = NULL] )
        >   將指定path 檔案加到ZIP內
        >   $filename 為抓取path目錄檔案，將其加入到ZIP檔案內
        >   $localname 為取代path的檔案名稱作為放入ZIP檔案內的檔案名稱

        (boolean) ZipArchive::addFromString ( string $localname , string $contents )
        >   將指定字串壓縮成ZIP內的單一檔案

        (boolean) ZipArchive::close ( void )
        >   將ZIP檔案關閉，這是結尾一定要做的
        >   如果此方法return false，代表有可能先前操作有新增不存在檔案或者path路徑錯誤
        >   可以利用file_exists() 或 is_readable()

----------------------------------------
            進階應用
----------------------------------------
- 傳輸層
```php
    // 宣告網頁header
    // 網頁編碼的 header 會影響到 mysql 在讀取資料庫資料時，使用什麼編碼解析資料
    header('Content-Type: text/html; charset=UTF-8');
    header('Content-Type: text/html; charset=big5');
    // 轉網址至某某地方
    header("Location: tpqe_bill.php?jobno=$jobno");
```
- CORS 跨網域
```php
    header("Access-Control-Allow-Origin: *");
    header("Access-Control-Allow-Headers: Content-Type");
```
- 額外function
```php
<?php
    function strip_tags_content($text, $tags = '', $invert = false)
    {
        preg_match_all('/<(.+?)[\s]*\/?[\s]*>/si', trim($tags), $tags);
        $tags = array_unique($tags[1]);

        if (is_array($tags) and count($tags) > 0) {
            if ($invert == false) {
                return preg_replace('/<(?!(?:' . implode('|', $tags) . ')\b)(\w+)\b.*?>.*?</\1>/si', '', $text);
            } else {
                return preg_replace('/<(' . implode('|', $tags) . ')\b.*?>.*?</\1>/si', '', $text);
            }
        } elseif ($invert == false) {
            return preg_replace('/<(\w+)\b.*?>.*?</\1>/si', '', $text);
        }
        return $text;
    }
?>
```

- HTML 標籤處理
```php
    // Sample text:
    $text = '<b>sample</b> text with <div>tags</div>';

    strip_tags($text):
    // sample text with tags

    strip_tags_content($text):
    // text with

    strip_tags_content($text, '<b>'):
    // <b>sample</b> text with

    strip_tags_content($text, '<b>', TRUE);
    // text with <div>tags</div>
```

----------------------------------------
            變量引用
----------------------------------------
- Function arguments
    - function (&$傳入參數)
    >   參數前面加上 &，即便沒有return 在function 對加上&的參數作變更，將直接影響到傳入的參數(被指向同一個地方)
    >   也就是function 結束後，外部變數也會維持被變更後的結果

    - global $外部變數;
    >   另一方式是在 function 內使用 global，但這是較不好的作法，因為使用 global 後，函數內的對變數變更將會變更外部參數
    >   若專案巨大且頻繁使用，會不知道更改變數的位置，導致除錯困難

    - 動態變數(Variable-length argument lists)
    >   必須為 PHP 5.6 以上版本
    >   在變數前面加上 ... 讓變數變成動態長度
    ```php
        // php 5.6+
        function sum(...$numbers) {
            $acc = 0;
            foreach ($numbers as $n) {
                $acc += $n;
            }
            return $acc;
        }

        echo sum(1, 2, 3, 4);

        // older version
        function sum() {
            $acc = 0;
            foreach (func_get_args() as $n) {
                $acc += $n;
            }
            return $acc;
        }

        echo sum(1, 2, 3, 4);
    ```

    - (int) func_num_args ( void )
    >   回傳 function 參數數量

    - (array) func_get_args ( void )
    >   回傳 function 參數，並將其轉成陣列型態

- include_once
    >   將會引用程式碼一次，若已被引用則不會再次引用

- require_once
    >   與include_once相同，差別為找不到檔案時，將會回傳錯誤，程式終止不運行

----------------------------------------
            寫法邏輯
----------------------------------------
- 可以先將計算結果存入字串，需要的時候再輸出
- 多加利用陣列配合 foreach 可以減少重複性的程式碼
- PHP Default Argument Value
    若PHP function 參數先給定植，則在參數無傳值時才會給予預設值
    否則依據傳入值做計算
    例：
        function setHeight($minheight = 50)
        setHeight(350);
        setHeight(); // will use the default value of 50

- 字串長度過長，需截斷輸出：
```php
    for ($i=0; $i < mb_strlen($value, "BIG5"); $i += 60) {
        print "<pre>" . mb_substr($value, $i ,60, "BIG5") . "</pre>";
    }
```

- 傳送巨量資料時，過大的array或者資料對PHP的memory都是巨大的負擔，若會處理到大量資料時，應該將資料切割處理
- 陣列耗費的資源比字串還大，若可以將陣列資料轉換為字串傳送，則盡量使用之


----------------------------------------
            OOP
----------------------------------------
- 物件引用
    兩個冒號（::)是對類中的方法的靜態引用
    與 (->) 比較來說
    也就是不需要實例化對象(不需要new)，直接通過類名對類中的方法進行引用

- namespace 命名空間

- ReflectionClass 反射類別
    >   可以得到Class的各種屬性，例如
    ```php
    $mysqlic = new ReflectionClass('mysqli');
    // 得到 mysqli 類別的複製
    $mysqli = $mysqlic->newInstanceArgs($params);
    ```
## CLass ##
定義 class 方法，使用 new Class() 實作出 object


1. 抽象方法
    在類中，沒有方法體的方法就是抽象方法。
    ```php
    abstract 可見性 function 方法名稱(參數1,.....);      // 如果沒有察看地指定可見性，則預設為public
    // 例：
    public function hello($args);
    // 修飾符abstract，也可以省略
    abstract function work();
    ```

2. 抽象類
    ```php
    abstract class 類名{
            屬性;
            方法;
            抽象方法;
    }
    ```

## 抽象類的特點：##
- 抽象類無法實例化，只能被繼承。
- 抽象類不一定有抽象方法，有抽象方法的類，一定是抽象類。
- 抽象方法的可見性無法是private
- 抽象方法在子類中，需要重寫。

## 什麼時候需要用抽象類？##
- 有個方法，方法體不知如何寫，子類中還必須有這個方法時，封裝成抽象方法，類為抽象類。
- 控制子類中必須封裝某些方法時，可以用抽象方法。
- 當需要控制類只能被繼承，無法被實例化時。

**例子：**
```php
    // 聲明一個人類，有一個抽象方法，工作。
    // 聲明一個php講師類，重寫方法工作。
    abstract class People {
        protected $age=22;
        public $height=1.70;
        abstract function work();
    }
    class PhpTeacher extends People{
        function work(){
            echo "真不是php";
        }
    }
```

3. 介面
如果一個類中，所有的方法都是抽象方法，且沒有成員屬性，則這個類被稱為介面（interface）。
```php
interface Common {
    abstract function work();
    abstract function test($args);
}
```

介面的作用：雖然PHP的類是單繼承，但**可以通過介面來實現多繼承**。

介面的繼承（extends）：
介面繼承介面
```php
interface 介面名稱 extends 父介面名稱
```
注意：類的繼承是單繼承（只能有一個父類），但介面的繼承卻是多繼承，類對介面的實現也是多實現。

介面的實現（implements）：
類實現介面
```php
class 類名 implements 介面名稱1,介面名稱2, ...
```

繼承類同時實現介面：
類繼承父類同時實現介面
```php
class 類名 extends 父類名 implements 介面名稱
```

4. 抽象類和介面的區別
- 介面是一種特殊的抽象類，介面中只包含抽象方法，沒有成員屬性。
- 類實現（implements）介面時，必須完全實現介面中的所有方法；
- 類繼承（extends）抽象類時，只需對需要用到的抽象方法進行重寫。
- 抽象類只能單繼承，但介面卻是多繼承，類對介面的實現也是多實現。

----------------------------------------
            資料庫操作
----------------------------------------
- mysqli
    - 僅針對mysql系列資料庫，若不更換資料庫類型可使用
    **object method**

    **function**
    - mysqli_connect (server IP, UserId, UserPass, database Name)
        >   創建資料庫連線
    - mysqli_fetch_row 將資料庫的fetch 為陣列，陣列的key會依數字遞增，KEY由0開始
        >   若在搜尋資料時，資料庫找不到資料，則在fetch的過程中會回傳false
    - mysqli_fetch_assoc 將資料庫的fetch 為陣列，陣列的key會依據欄位名稱命名
        >   若在搜尋資料時，資料庫找不到資料，則在fetch的過程中會回傳false
    - mysqli_fetch_array 將資料庫的fetch 為陣列，陣列會有兩種KEY可使用，一個為數字遞增KEY，一個為SQL欄位名稱
        >   與 mysqli_fetch_assoc 類似，但由於會產生兩個KEY，故效率較差，盡可能只選擇一種即可
    - mysqli_insert_id
        >   取得 auto generated id in last query
    - mysqli_num_rows
        >   取得 result set 的列數
    - mysqli_close($db);
        >   關閉連接，但要注意，不要在require或include頁面後做，以避免require的連線被關閉，前後關聯需要注意清楚

- PDO (PHP Data Objects)
    - 支援大部分主流資料庫，若更換資料庫可改寫較少程式碼
    - prepared statement 支援較佳

- query內若要新增query字串內容時，則需使用  $query = "XXXXX'{OOO["KEY"]}'XXXXX";

----------------------------------------
            安全性寫法
----------------------------------------
- 使用超全域變數時 ($GLOBALS, $_POST, $_GET 等等)，必須使用htmlentities()，避免特殊字元，令駭客找出漏洞

- 使用 include php 網頁時相關技巧；
    >   可以將需要被include的網頁放置在document root之外的資料夾內
    >   再使用
        ```php
        include __DIR__ . '/../src/includeFile.php';
        ```
    >   因為user不能使用'your.site/../src/includeFile.php'去瀏覽你的頁面

- 密碼驗證
    password_hash ( string $password , int $algo [, array $options ] ) : `string`
    >   最好的資料長度建議為255
    >   預設的方式將會動態產生 salt，此值是動態加入以擾亂辨識原本的密碼型態 (避免有人使用先算好的雜湊值碰撞硬解)
    password_verify ( string $password , string $hash ) : *boolean*
    >   可以驗證經由 password_hash 產生出來的亂數

----------------------------------------
            系統紀錄參考
----------------------------------------
```php
// 非正式寫法
# ---------------------------------------------------------------------------------------------
# System Name : XXX管理系統
# Prog.  Name : cr_rocfee_cu.php
# Upper Layer : MT_fee_search.php
# Next  Layer : none
# Author      : XXX
# Finish Date : 2017/09/15
# ---------------------------------------------------------------------------------------------
# Modify Rec. : 紀錄參數...相關說明
# ---------------------------------------------------------------------------------------------
```

----------------------------------------
        PHP 相關套件語法使用
----------------------------------------
### TCPDF ###
```php
//Cell($w, $h=0, $txt='', $border=0, $ln=0, $align='', $fill=0, $link='', $stretch=0, $ignore_min_height=false, $calign='T', $valign='M')

```

### PHP WORD ###

----------------------------------------
        PHP 棄用函式以及取代方案
----------------------------------------
**PHP 5.3 後棄用，7.0後移除**
- eregi ( string $pattern , string $string [, array &$regs ] ) : `int`
    >   用正規表示式尋找需要尋找的物件內是否有包含某字串
    >   官方建議替代function為`preg_match()`
    ```php
    preg_match("/php/i", "PHP is the web scripting language of choice.");
    ```

- ereg_replace ( string $pattern , string $replacement , string $string ) : `string`
    >   用正規表示式尋找需要尋找的物件內是否有包含某字串，並將該字串取代
    >   官方建議替代function為`preg_replace()`
    ```php
    // 範例
    ereg_replace("[0-9]", "", $number);
    // 替代為
    preg_replace("/[0-9]/", "", $number);
    ```

- split ( string $pattern , string $string [, int $limit = -1 ] ) : `array`
    >   可以用正規表示法切開字串產生陣列
    >   官方建議替代function
    >   `explode()` 不使用正規表示法，使用某特定字符作為切開字串
    >   `preg_split()` 比較像原本的功能，使用正規表示法切開字串
    >   `str_split()`