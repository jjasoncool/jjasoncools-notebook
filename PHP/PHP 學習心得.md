# 一般語法參考 #

## cli (command line 部分) ##
  - -v 查看目前版本
  - --ini 可以查看目前php 使用哪個設定檔
  - -c (ini路徑) 可以指定 php 使用哪個地方的設定檔
    - 多版本PHP暫時找到的替代執行方式，linux可以透過 configure 指定 php.ini 位置
    - 但主要的 path 設定還是需要找到共存方法
    - `D:\xampp\php-7.3.14\php -c "D:\xampp\php-7.3.14\php.ini" D:\xampp\php-7.3.14\composer.phar`
  - -r 可以直接執行 php 語法，Run PHP code without using script tags <?..?>
    - `php -r "print_r(openssl_get_cipher_methods());"`

## 實用常數 ##
  - **PHP_EOL** 依照平台不同使用不同的 End Of Line symbol
  - **PHP_OS** 回傳作業系統類型，可以拿來判斷 linux 或是 windows
  - **DIRECTORY_SEPARATOR** 依據作業系統使用不同分隔符
  - **$_SERVER['HTTPS']** 可以拿來判斷伺服器是否有開啟 https

## super globals(超域變數) ##
- `$_SERVER` is an array containing information such as headers, paths, and script locations.
  - 若 PHP 以 command line 執行，則部分參數將不存在，例如: SERVER_NAME, DOCUMENT_ROOT, HTTP_HOST 等等與瀏覽器相關變量
- `$_GET` 當使用 URL 的變數(query string)請求時，可以取出的關聯變數陣列
- `$_POST` 當表單使用 application/x-www-form-urlencoded 或 multipart/form-data 當作編碼並使用 HTTP POST 方法請求時，將會得到的關聯變數陣列
- `$_FILE` 當物件使用 POST 方法被上傳至該php腳本時，此陣列將會產出該物件的狀態與相關資訊，包含以下:
  - *name*: 原始檔名
  - *type*: mime 類型
  - *size*: 檔案大小(以byte計)
  - *tmp_name*: 暫時存在伺服器上的暫存檔檔名
  - *error*: 上傳中有無任何錯誤
    ```php
    $phpFileUploadErrors = array(
        0 => 'There is no error, the file uploaded with success',
        1 => 'The uploaded file exceeds the upload_max_filesize directive in php.ini',
        2 => 'The uploaded file exceeds the MAX_FILE_SIZE directive that was specified in the HTML form',
        3 => 'The uploaded file was only partially uploaded',
        4 => 'No file was uploaded',
        6 => 'Missing a temporary folder',
        7 => 'Failed to write file to disk.',
        8 => 'A PHP extension stopped the file upload.',
    );
    ```
  - `$_COOKIE` An associative array of variables passed to the current script via HTTP Cookies.
  - `$_SESSION` An associative array containing session variables
  - `$_ENV` 與環境變量之相關的關聯陣列，若是空值，則與 php.ini variables_order 設定有關
    - getenv ( string $varname [, bool $local_only = FALSE ] ) : `string`
      - 取得目前 server 設定的環境變數，亦可取得使用 putenv 設定之變數
    - putenv ( string $setting ) : `bool`
      - 設定暫時的環境變量，該 script 執行完成後，環境變量將變回初始值
      - 設定方式像是
        ```php
        putenv("FOO=BAR");
        // 空白也會被當作變數名，故此變量與上面的變量不同
        putenv("FOO =BAR");
        // set MYVAR to an empty value.  It is in the environment
        putenv('MYVAR=');
        // unset MYVAR.  It is removed from the environment
        putenv('MYVAR');
        ```

## 整齊的寫法 ##
- 運算子前後一定要有空白
- PHP tag 一定單獨佔據一行，並且在Code的最左方
- PHP tag 若在整個都是PHP的文件內，必須省略最後的 ?>
```php
    <?php
        blah
    ?>
```
## 命名規則 ##
- 駝峰式寫法(camelCase CamelCase)
- Class ClassName
- function functionName
- parameter parameterName

## 編碼轉換 ##
  - iconv ( string $in_charset , string $out_charset , string $str ) : string
    >   iconv ("BIG5","UTF-8",$string)
    >   iconv( 來源編碼 , 輸出編碼[//參數:可加可不加] , 字串 )
    >   //TRANSLIT會自動將不能直接轉換的字符變成一個或多個近似的字符，
    >   //IGNORE會忽略掉不能轉化的字符，而默認效果是從第一個非法字符截斷。
    >   預設不加的話，只要有非法字元，整句字串都不會輸出

  - mb_convert_encoding ( $string || $array , string $to_encoding [, mixed $from_encoding = mb_internal_encoding() ] ) : mixed
    >   mb_convert_encoding ($string || $array , "UTF-8", "BIG5" )
    >   mb_convert_encoding( 字串 , 輸出編碼 , 來源編碼 )
    >   這個編碼模式將會將各種無論是否非法字符強制轉換，但效能較差
    >   PHP 7.2 之後可以支援 array 傳入

  - urlencode ( string $str ) : string
    >   **~** 此符號在 php5.3後不再轉換為編碼
    >   將文字編碼轉換為URL編碼
    >   範例:%E9%9B%BB%E8%85%A6%E7%9B%B8%E9%97%9C%E8%A8%AD%E5%82%99%E7%94%B3%E8%AB%8B%E8%A1%A8(電腦相關設備申請表)
    >   問題在於空格會被轉換成 **+** 號，若需要完整的轉換請改用 `rawurlencode()`

  - rawurlencode ( string $str ) : string
    >   功能同上述，但不會有符號混淆問題，與javascript 交換JSON 資料時可以避免特殊符號導致錯誤的情形
    >   遵守 RFC 3986 編碼規則

  - urldecode ( string $str ) : string
    >   將所有網址列內%開頭的文字解碼為可讀文字
    >   **+** 號將被解碼為空格
    >   $_GET 與 $_REQUEST 已有解碼，若使用此函數在此兩個全域變數上，可能會發生無法預期的結果

  - rawurldecode ( string $str ) : string
    >   將所有網址列內%開頭的文字解碼為可讀文字
    >   特殊符號部分如 + 號或者 ~ 將可以正常轉換
    >   對應 javascript `encodeURIComponent()`

## 字串 ##
  - 字串基本常識

        單引號(Single quotes) 內不可以放變數，會將所有東西直接輸出的字串
        雙引號(Double quotes) 內可以直接放變數，會解析帶有$符號的變數
        ※注意: 使用雙引號的變數，可以使用{}大括弧包起來，這樣便可以不需要使用連結符號 . 即可連接變數變成字串
        heredoc syntax 內部也可以放變數，但常數無法放

    ```php
    <?php
    // Use double quotes to concat more than two strings instead of multiple '.' operators.
    // PHP is forced to re-concatenate with every '.' operator.
    "{$str1}{$str2}{$str3}"; // one concat = fast
    $str1 . $str2 . $str3;   // two concats = slow
    <<<EOD
    foo bar
    EOD;

    ?>
    ```
  - strtolower ( string $string ) : string
  - mb_strtolower ( string $str [, string $encoding = mb_internal_encoding() ] ) : `string`
    >   將字串變為小寫
    >   mb_strtolower 可針對一些特殊拉丁語系轉為正確小寫

  - strtoupper ( string $string ) : string
  - mb_strtoupper ( string $str [, string $encoding = mb_internal_encoding() ] ) : `string`
    >   將字串變為大寫
    >   mb_strtoupper 可針對一些特殊拉丁語系轉為正確大寫

  - ucwords ( string $str [, string $delimiters = " \t\r\n\f\v" ] ) : `string`
    >

  - mb_convert_case ( string $str , int $mode [, string $encoding = mb_internal_encoding() ] ) : `string`

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

  - strcmp ( string $str1 , string $str2 ) : `int`
    >   比較字串是否相等
    >   回傳值如果為0則字串相等
    >   如果字串1比字串2短，回傳值小於0
    >   如果字串1比字串2長，回傳值大於0
    >   通常可以用於字串長度排序


  - strlen ( string $string ) : `int`
  - mb_strlen ( string $str [, string $encoding = mb_internal_encoding() ] ) : `int`
    >   回傳字串長度
    >   mb_strlen 為可指定編碼

  - (int) preg_match ( "正規表示式尋找字串" , "被尋找字串" [, array &$matches [, int $flags = 0 [, int $offset = 0 ]]] )
    >   回傳匹配字串位置，使用正規表示式
    ```php
    preg_match("/^[0-9]{4}-(0[1-9]|1[0-2])-(0[1-9]|[1-2][0-9]|3[0-1])$/",$checkdate)
    ```

  - ucfirst ( string $str ) : string
    >   將英文字母第一個字換成大寫，可以配合lowercase

  - lcfirst ( string $str ) : string
    >   將英文字母第一個字換成小寫，可以配合uppercase

  - eval ( string $code ) : mixed
    >   字串中不要包含 <?php ?> 標籤
    >   版本 PHP7 後，若有語法錯誤，將會觸發 ParseError exception
    >   回傳值為 null ,若字串中包含 return，將會回傳字串作為PHP運算後得到之結果 例如：
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

  - mb_split ( string $pattern , string $string [, int $limit = -1 ] ) : `array|false`

  - preg_split ( string $pattern , string $subject [, int $limit = -1 [, int $flags = 0 ]] ) : `array|false`
    >

  - str_replace ( mixed $search , mixed $replace , mixed $subject [, int &$count ] ) : `string/array`
    >   可使用陣列方式取代字串值， $search 將會鏡像對應 $replace 值，若 $replace 數量少於 $search ，其餘被取代字串將會被替代為空字符串
    >   使用陣列串值時，將會由第一個開始依序取代字元，例如 a 改成 apple，p 改成 pear，字串輸入 a p ，期望結果是 apple pear，但實際結果會是 apearpearle pear，可參考 **strtr()**
    >   $replace 若只有一個，則所有的 $search 陣列內元素將會被取代為 $replace 字串
    >   count若有傳入參數，該參數將會被設值，該值為取代了多少結果，可於後續取出查看

  - preg_replace ( mixed $pattern , mixed $replacement , mixed $subject [, int $limit = -1 [, int &$count ]] ) : `string/array`
    >   基本使用上同上 str_replace，但由正規表示法來做搜尋
    >   此法可限制取代之數量為何，預設 $limit -1 沒有限制數量
    >   count若有傳入參數，該參數將會被設值，該值為取代了多少結果，可於後續取出查看
    >   `preg_replace("/[^0-9]/", "", "Every 6 Months" );`

  - strtr ( string $str , string $from , string $to ) `string`
  - strtr ( string $str , array $replace_pairs ) `string`
    >   此函數功能為，依照字串對應更新，或者使用陣列，將對應key值置換為value值
    >   如果 **from 與 to 長度不相等**，那麼多餘的字符部分將被忽略。 str 的長度將會和返回的值一樣
    >   此方法不會重複更新，只會搜尋有對應的值做一次取代 (這是與 **str_replace()** 的不同點)

  - htmlentities ( string $string [, int $flags = ENT_COMPAT | ENT_HTML401 [, string $encoding = ini_get("default_charset") [, bool $double_encode = TRUE ]]] ) : `string`
    html_entity_decode ( string $string [, int $flags = ENT_COMPAT | ENT_HTML401 [, string $encoding = ini_get("default_charset") ]] ) : `string`
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
    >   `$encoding 5.6版本以後預設為php.ini中的default_charset, 5.5~5.4預設為UTF8, 5.3以前預設為latin1*`
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

  - stripslashes ( string $str ) : `string`
    >   將跳脫字元的字符還原成沒有跳脫的樣式
    >   與get_magic_quotes_gpc有關

  - nl2br ( string $string [, bool $is_xhtml = TRUE ] ) : `string`
    >   幫助像是textarea的換行符 \n 轉換成為html <br>
    >   在5.3版本之後才有 $is_xhtml，若此值為true則返回<br />

  - strrchr ( string $被擷取字串 , mixed $needle ) : `string`
    >   返回最後 needle 的字串作為開始處，直到字串末尾
    >   可以用來做最後的檔名搜尋或是資料夾名稱確認

  - str_shuffle ( string $str ) : string
    >   將字串視為陣列隨機排序

## 資料處理 ##
  - imap_rfc822_parse_adrlist ( string $address , string $default_host ) : `array`
    >   將一串電子郵件地址轉換為 **ArrayObject** (PHP5 之後才有ArrayObject)
    >   可參考**class.phpmailer**內使用來檢驗email位址是否正確

  - 特殊處理 XML/HTML **DOMDocument class**
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

  - parse_str ( string $encoded_string [, array &$result ] ) : `void`
    >   極度不建議在沒有 result 參數的情況下使用此函數，並且在 PHP 7.2 中將廢棄不設定參數的行為
    >   主要用來轉換 querystring 變成陣列輸出
    >   若字串變數含有點或空格，將被轉為底線_

  - http_build_query ( mixed $query_data [, string $numeric_prefix [, string $arg_separator [, int $enc_type = PHP_QUERY_RFC1738 ]]] ) : `string`
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

## 數值 ##
  - intval ( mixed $var [, int $base = 10 ] ) : `int`
    >   轉換參數為數值，不可轉換物件，否則將跳出 notice，並返回數值 1
    >   預設為十進制
    >   可以轉換進位制

  - number_format ( float $number [, int $decimals = 0 ] ) : `string`
  - number_format ( float $number , int $decimals = 0 , string $dec_point = "." , string $thousands_sep = "," ) : `string`
    >   如果只給予1~2個參數，小數點為. 千分位為,
    >   決定數字顯示格式，第一個參數為數字變數、第二個參數為小數點數量、第三個參數為小數點顯示符號(依據國情不同)、第四個參數為千分位符號(依據國情不同) 預設四捨五入

## 陣列 ##
  - $變數 = array();
    >   要做push之前一定要先做宣告
    >   若要避免notice 可使用 array_fill()函數

  - current ( array $array ) : `mixed`
    >   Every array has an internal pointer to its "current" element, which is initialized to the first element inserted into the array.
    >   簡單而言，就是返回目前陣列指針指向之當前的值

  - list( $要塞入的變數 ) = $array
    >   將陣列中的值塞入變數之中
    >   list 只對 key 為數字型態的陣列生效，key從0開始
    >   php7 後，填入陣列內的排序由最左邊參數開始賦值，php5 之前則相反，不要使用此方法來做為排序的依賴

  - unset ( mixed $var [, mixed $... ] ) : void
    >   重置、刪除變數
    >   但如果在區域function 內 unset 公用變數，則不影響公用變數值，僅在function內unset而已
    >   若要在function 內 unset 公用變數值，則需使用 unset($GLOBALS["blah"])

  - count ( mixed $array_or_countable [, int $mode = COUNT_NORMAL ] ) : `int`
  - sizeof()
    >   計算陣列/物件大小、陣列/物件長度
    >   sizeof() 為 count() 的別名

  - asort ( array &$array [, int $sort_flags = SORT_REGULAR ] ) : `bool`
    >   對陣列做排序，關聯 key 值也會依據排序順序改變順序

  - array_multisort ( array &$array1 [, mixed $array1_sort_order = SORT_ASC [, mixed $array1_sort_flags = SORT_REGULAR ]], mixed ...$rest ) : bool
    >   rest 代表更多的陣列，若前面的陣列值相同時，對應該相同值後面的陣列會繼續排序
    >   可利用在多維陣列排序

  - compact ( mixed $var_name , mixed ...$var_names ) : `array`
    >   compact() 在当前的符号表中查找该变量名并将它添加到输出的数组中，变量名成为键名而变量的内容成为该键的值。
    >   變量未定義，則會出現 E_NOTICE 錯誤
    ```php
    $city  = "San Francisco";
    $state = "CA";
    $event = "SIGGRAPH";

    $location_vars = array("city", "state");
    $result = compact("event", $location_vars);
    // [
    //     [event] => SIGGRAPH,
    //     [city] => San Francisco,
    //     [state] => CA,
    // ]
    ```

  - array_fill ($開始的陣列index, $填滿幾個, $填入值) : `array`
    >   此函數會使用數字型態index 填滿指定範圍的陣列
    >   可利用來避免陣列notice，例：
    ```php
    $element = !empty( $_POST["dataString"] ) ? explode(",", $_POST["dataString"]) : array_fill(0, 5, '');
    ```
    >   亦可用來產出 sql prepared statement 的參數
    ```php
    $insParaStr = implode(",", array_fill(0, count($recArray), "?"));
    ```

  - array_push ( array &$array [, $值1, $值2, ...] ) : `int`
    >   將值塞入陣列內，但key 只會為數值，可與for迴圈合併使用，可一次塞入多項數值
    >   若並非要塞入多像數值，建議使用 $變數名稱[] = $值 ，效能較佳
    >   若要塞入的陣列沒有預先定義為array，則會跳出warning
    >   回傳值為成功新增的元素數量

  - array_unshift ( array &$array [, mixed $... ] ) : `int`
    >   新增項目到陣列清單的開頭
    >   將元素放至陣列前方，但順序會依照，傳入值的順序整體塞入前方

  - array_keys ( array $array ) : `array`
  - array_keys ( array $array , mixed $search_value [, bool $strict = FALSE ] ) : `array`
    >   回傳一個陣列的所有key值
    >   若有定義 search_value，此函數將返回符合該值的陣列keys

  - array_key_last ( array $array ) : mixed
    >   取得最新的 key 值，不影響指標

  - array_values ( array $array ) : array
    >   回傳一個陣列的所有value值
    >   可用來當作重新對數字的 key 重新編號的函數

  - array_column ( array $input , mixed $column_key [, mixed $index_key = NULL ] ) : `array`
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

  - implode ( string $接合字串 , array $陣列 ) : `string`
    >   回傳將陣列元素使用特定接合字串接合起來的長字串

  - array_filter ( array $傳入陣列 [, callable $被呼叫函數 [, int $flag = 0 ]] ) : `array`
    >   將陣列的每一個value值傳遞到可以被呼叫的函數內，若函數回傳值為true，則該值將會被保留在回傳陣列之內，且該回傳陣列key值與原先傳入陣列相同
    >   若沒有被呼叫的函數，則將會過濾掉等效為false值之陣列，若想保留0值時，可利用strlen保留值為0的元素 `array_filter($array, 'strlen')`

  - array_diff ( array $主要陣列 , array $參考陣列 [, array $... ] ) : `array`
    >   將主要陣列對照其他參考陣列，此函數回傳主要陣列中存在但不存在於其他參考陣列中的值
    >   也就是只有主要陣列中特殊值會被保留下來
    >   即使參考陣列中有其他主要陣列沒有的值，也不會被回傳

  - array_diff_key ( array $主要陣列 , array $參考陣列 [, array $... ] ) : `array`
    >   同上，只是將比較的物件換成 key

  - array_combine ( array $keys , array $values ) : `array`
    >   合成兩個陣列，成為一個索引與值互相對應的陣列
    >   其中一個陣列當 key 值，另外的陣列當作陣列值

  - array_unique ( array $array [, int $sort_flags = SORT_STRING ] ) : `array`
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

  - array_flip  ( array $array ) : `array`
    >   陣列索引與值做交換

  - array_intersect_key ( array $array1 , array $array2 [, array $... ] ) : `array`
    >   以第1個陣列為基礎，將陣列2以後存在的key當作回傳的陣列key，並以這些key來取得陣列1的值
    >   陣列2以後的值不使用
    >   用來取得某固定範圍的key值做使用

  - array_search ( $要找尋的變數值, array $被搜尋的array [, bool $strict = FALSE ] ) : `mixed`
    >   搜尋陣列內是否有值，若有則回傳該陣列的key值，若有重複符合值，回傳第一個符合值的 key。
    >   若您想要回傳的結果包含所有 key 值的陣列，請使用 **array_keys()**
    >   可配合 **array_column()** 搜尋二維陣列，或是使用 foreach 迴圈的 $value 放入array_search尋找
    >   `$first_key = array_search($value, array_column($multi_array, 'second_key'))`
    >   若該變數沒有在陣列中被被搜尋到，會返回 false 值


## 判斷類 ##
  - boolval ( mixed $var ) : `bool`
    >   將輸入參數轉為 boolean 值，最常見為0和1轉為false和true
    >   **注意: 負數在 boolean 裡面被判斷為 true**

  - empty ( mixed $var ) : `bool`
    >   判斷陣列或值是否為空值或者是0，其他值則回傳FALSE

  - isset ( mixed $var [, mixed $... ] ) : `bool`
    >   判斷值是否存在，若為NULL或者不存在，則回傳FALSE，其他為TRUE

  - in_array ( 混合型別 $要找尋的變數 , array $於哪個陣列搜尋 [, bool $strict = FALSE ] ) : `bool`
    >   第三個變數預設為FALSE，不比較型別，但有人指出不使用strict 方式，將會出現部分錯誤
    >   此函數為比對在array 內的物件是否存在，並返回 boolean 值

  - is_numeric ( mixed $var ) : `bool`
    >   判斷為純數字時，輸出true

  - array_key_exists ( mixed $key , array $被搜尋的陣列 ): `bool`
    >   傳入值若沒有在陣列之中，則回傳false

## 迴圈與跳出 ##
  - return
    >   回到原本呼叫的模組
    >   若是在 include 或者被 require 的頁面，使用 return 將會返回該引用頁

  - break
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

## 日期與時間處理 ##
  - ※注意:2038 timestamp 會溢出32位元極限，因此要使用 timestamp 相關函數需要注意。
    (timestamp) strtotime()
    >   回傳時間戳
    (timestamp) mktime($時, $分, $秒, $月, $日, $年, $是否為日光節約時間 = -1);
    >   此函數可利用來計算時間區間，因使用時間戳記，故就算在函式內加減都沒有問題
    >   經測試 mktime($時, $分, $秒, $月=date("n"), $日=date("j"), $年=date("Y")) 時，取得現在時間可將值代0，但年月日請不要代0，須代入date函數，避免錯誤
    >   另外值內可以做加減 (不過如果要計算時間日期區間，建議使用MYSQL的function比較不會有問題)

    date_default_timezone_set ( string $timezone_identifier )
    >   在其中一支程式內套用選定時區，不影響其他程式碼

  - DateTimeImmutable 物件與 DateTime 有何不同?
    - DateTime behaves the same as DateTimeImmutable except objects are modified itself when modification methods such as DateTime::modify() are called.
    - 從字面上解釋，不難理解 DateTimeImmutable 為保留初始值的作法，常常發現 DateTime 物件往往經過方法修改過後，一開始定義的初始值也會被連同修改，在複雜的程式碼經過多次運算後，往往會造成問題


  - DateTime Object
    (object)
    ```php
    // 單行輸出
    (new DateTime())->format('Y-m-d H:i:s');
    // 新增 datetime 物件 (php 5.2 可以用的日期物件)
    $now = new DateTime();
    $temp_time = new DateTime($row['temp_time']);
    $temp_time->modify('+3 day');
    echo $temp_time->format('Y-m-d H:i:s');
    echo $now < $temp_time;

    // 修改日期利用 date (P{y}Y{m1}M{d}DT{h}H{m2}M{s}S)
    // For example, P1Y2DT5S means 1 year, 2 days, and 5 seconds.
    $temp_time->add(new DateInterval('PT' . $minutes_to_add . 'M'));

    // 輸出日期差異總天數
    $date_end->diff($date_start)->format('%a');

    // DateTime 常用的函數 (php 5.3以後)
    // 轉換成DateTimeImmutable
    $date = new DateTime("2014-06-20 11:45 Europe/London");
    $immutable = DateTimeImmutable::createFromMutable($date);

    // DateTimeImmutable 常用的函數 (php 5.5以後)
    // 轉換成DateTime
    $date = new DateTimeImmutable("2014-06-20 11:45 Europe/London");
    $mutable = DateTime::createFromImmutable( $date );

    ```

## 錯誤處理 ##
  - set_error_handler ( callable $error_handler [, int $error_types = E_ALL | E_STRICT ] ) : mixed
    >   設定用戶的函數 (error_handler) 來處理程式檔中出現的錯誤。
    >   本函數可以用你自己定義的方式來處理執行中的錯誤， 例如，在應用程式中嚴重錯誤發生時，或者在特定條件下觸發了一個錯誤(使用 trigger_error())，你需要對資料/檔案做清理回收。
    - handler ( int $errno , string $errstr [, string $errfile [, int $errline [, array $errcontext ]]] ) : bool
    >   此為回調函數，當錯誤發生時將會把當前的狀態傳入並提供參數

## 其他應用 ##
  1. Variable functions
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

  - call_user_func ( callable $callback [, mixed $parameter [, mixed $... ]] )
  - call_user_func_array ( callable $callback , array $param_arr )
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

  2. 動態陣列
      - 陣列內的值可以用 &$變數(指向同一記憶體位址) 但目前似乎不能串接字串，只能是單獨值，因此必須使用一個欄位來做承接
        >   array1(&$test)
        >   array2(&$test)
        >   function (array1 , array2) .... 修改 $test 值，兩個陣列的值會同步變更，至於 $test 是否需宣告，則看是否有將此參數傳進入function(&$test)
        >   若僅是針對 array[0] 對址修改，其實test要取甚麼名字都無所謂(也無須宣告)

      - array_map ( callable $callback , array $array , array ...$arrays ) : `array`
        >   可以在第一個參數放callable function (也就是有回傳值的函數)
        >   此函數將會將每一個 array 值，都透過 $callback 處理過一次，返還array
        >   若callback函數需要有兩個參數，則array1需對應callback的參數數量放置對應的array
        ```php
        $rates = array_map(function($value) {
            return $value === "-" ? NULL : $value;
        }, $rates);
        ```

      - array_walk ( array &$array , callable $callback [, mixed $userdata = NULL ] ) : `boolean`
        >   callback 會取得兩個參數，第一個是陣列的值，第二個為陣列的索引值，且陣列值為 call by reference
        >   也就是說，在 callback function 內，陣列值的任何改變都會改變原輸入陣列
        >   只有 array 的值才可以被改變，用戶不應在回調函數中改變該陣列本身的架構。例如增加/移除元素，unset 元素等等。如果 array_walk() 作用的陣列改變了，則此函數的的行為未經定義，且不可預期。
        >   如果提供了可選參數 userdata，將被作為第三個參數傳遞給 callback function。
        ```php
        array_walk($array, function(&$val, $key) {
            $val = array_intersect_key($val, array_flip(['a', 'b', 'c', 'd']));
        });
        ```

      - array_walk_recursive ( array &$array , callable $callback [, mixed $userdata = NULL ] ) : `boolean`
        >   基本上同 array_walk，不同點為 array_walk 僅在第一層做處理，recursive 將會遍歷所有陣列
        >   注意下例中的鍵 'sweet' 並沒有察看出來。任何其值為 array 的鍵都不會被傳遞到回調函數中去。
        ```php
        $sweet = array('a' => 'apple', 'b' => 'banana');
        $fruits = array('sweet' => $sweet, 'sour' => 'lemon');
        function test_print($item, $key)
        {
            echo "$key holds $item\n";
        }
        array_walk_recursive($fruits, 'test_print');
        ```

  2. 資料編碼
     - 資料透過陣列轉換成json字串，或將json字串轉換成陣列
     - json_encode( mixed $value [, int $options = 0 [, int $depth = 512 ]] );
        >   通常為將陣列轉為json字串，也可以使用物件
        >   PHP5.4 支援 JSON_PRETTY_PRINT
        >   PHP5.4之後，UTF-8的字串會被escape，若不要被escape，可用選項 JSON_UNESCAPED_UNICODE
        >   若要直接輸出至 javascript 內，可使用
     - json_decode( string $json [, bool $assoc = FALSE [, int $depth = 512 [, int $options = 0 ]]] );
        >   $assoc 設為 true 時，轉換時的key將會是根據陣列的key值決定，否則為0123

## 變數 ##
  - 動態變數
    Using ${} is a way to create dynamic variables
    動態產生變數，可連結字串

  - Variable scope
    - global $外部變數;
    >   另一方式是在 function 內使用 global，但這是較不好的作法，因為使用 global 後，函數內的對變數變更將會變更外部參數
    >   若專案巨大且頻繁使用，會不知道更改變數的位置，導致除錯困難

    - static $靜態變數;
    >   變數範圍的另一個重要特徴是靜態變數（static variable）。
    >   靜態變數僅在局部函數域中存在，但當程式執行離開此範圍時，其值並不會消失。
    >   但離開局部函數時，無法對其取值

  - Function arguments
    - function (&$傳入參數)
    >   參數前面加上 &，即便沒有return 在function 對加上&的參數作變更，將直接影響到傳入的參數(被指向同一個地方)
    >   也就是function 結束後，外部變數也會維持被變更後的結果

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
    >   在function內使用，回傳 function 參數數量
    >   若在 global 區間使用將會出錯

    - (array) func_get_args ( void )
    >   在function內使用，回傳 function 參數，並將其轉成陣列型態
    >   若在 global 區間使用將會出錯


## 路徑處理 ##
  - pathinfo ( string $path [, int $options = PATHINFO_DIRNAME | PATHINFO_BASENAME | PATHINFO_EXTENSION | PATHINFO_FILENAME ] ) : `array/string`
    >   回傳路徑相關資料\
    >   若沒有指定 option 回傳陣列\
    >   filename 是5.2以後有的選項不須擔心，為避免健忘 **basename($filename, '.html');** 可以達到類似效果\
    >   pathinfo() 是地區編碼性的，若是混了了非支援的編碼，需要先做 `setlocale(LC_ALL, 'zh_TW.UTF-8');`

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

  - realpath ( string $path ) : `string`
  - sys_get_temp_dir ( void ) : `string`
    >   回傳 temp 的資料夾路徑，回傳的字串最後沒有包含資料夾分隔符號\
    >   例：`/home/user/tmp`

## 目錄操作 ##
- 目錄操作 Class Directory
- method:
  - read: Same as closedir()
  - close: Same as readdir()
  - rewind: Same as rewinddir()

- 相關 functions:
  - chdir
    >   修改目前工作目錄

  - getcwd ( )
    >   取得目前工作目錄

  - is_dir ( string $filename ) : `boolean`
    >   確認該路徑為目錄\
    >   Returns TRUE if the filename exists and is a directory, FALSE otherwise.

  - scandir ( string $directory [, int $sorting_order = SCANDIR_SORT_ASCENDING [, resource $context ]] ) : `array`
    >   將目錄中的所有檔案列出，但會包含兩個目錄也被列出 ./ ../ ， 可利用array_diff或array_slice去除\
    >   $sorting_order 可影響檔案讀出來的排列方式，基本上不會動 (以利套用array_slice)

  - readdir ([ resource $dir_handle ] ) : `string`
    >   需使用經由 opendir() 方法的 resource

  - mkdir ( string $pathname [, int $mode = 0777 [, bool $recursive = FALSE [, resource $context ]]] ) : `boolean`
    >   建立目錄\
    >   $mod 為 linux 權限，預設為 0777，**注意：0777 不可為字串**
    >   $recursive 是否可以迴圈創立內部子資料夾
    >   Returns TRUE on success or FALSE on failure.

## 檔案操作 ##
  - ini_get('upload_max_filesize')
    >   抓取php.ini 的資料，此處為抓取可上傳的檔案容量
    >   post_max_size 如果檔案上傳透過form post，則需要設定此值大於或等於upload_max_filesize

  - file_put_contents ( string $filename , mixed $data [, int $flags = 0 [, resource $context ]] ) : *boolean*
    >   如果檔案不存在將會創建一個新檔案，存在將會覆寫，除非有 flag 使用 FILE_APPEND

  - file_get_contents ( string $filename [, bool $use_include_path = false [, resource $context [, int $offset = -1 [, int $maxlen ]]]] ) : `string`
    >   抓取網址或者文本的方法
    >   若是要做為執行某PHP的方式，盡量還是用CURL，速度差10倍
    >   若是要 include 別的網頁，且對方有開啟 https，會需要驗證時，可以參考以下方法
    ```php
    $arrContextOptions = [
        "ssl" => [
            "verify_peer" => false,
            "verify_peer_name" => false,
        ],
    ];
    file_get_contents($網址, false, $arrContextOptions);
    ```

  - file_exists ( string $filename ) : *boolean*
  - is_file ( string $filename ) : *boolean*
    >   可以判斷檔案是否存在，但會寫入catch，可用clearstatcache()清空快取資料
    >   PHP 的 is_file() 及 file_exists() 都是用作檢查檔案是否存在，它們的分別是 file_exists() 輸入的參數是目錄也會回傳 TRUE，而 is_file() 則只會對檔案回傳 TRUE

  - unlink ( string $filename [, resource $context ] ) : *boolean*
    >   刪除檔案，刪除成功將會回傳 boolean 值

  - rename ( string $oldname , string $newname [, resource $context ] ) : *boolean*
    >   重新命名檔案，成功與否回傳boolean

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

  - mime_content_type ( resource|string $filename ) : string|false
    >   下載檔案依據檔名產出該檔案應該有的 mime

## 加解密與壓縮處理 ##
  - base64_encode ( string $data ) : `string`
  - base64_decode ( string $data [, bool $strict = FALSE ] ) : `string`
    >   可將二進位資料壓縮成僅用英文以及符號傳遞的加密字串，Base64可以用來將binary的位元組序列資料編碼成ASCII字元序列構成的文字
    >   使用的字元包括大小寫拉丁字母各26個、數位10個、加號+和斜槓/，共64個字元，等號=用來作為字尾用途

  - gzcompress ( string $data [, int $level = -1 [, int $encoding = ZLIB_ENCODING_DEFLATE ]] ) : `string`
  - gzuncompress ( string $data [, int $length = 0 ] ) : `string`
    >   壓縮字串的函式
    >   $level 壓縮等級，最高為9，最低為0

  - openssl_encrypt ( string $data , string $method , string $key [, int $options = 0 [, string $iv = "" [, string &$tag = NULL [, string $aad = "" [, int $tag_length = 16 ]]]]] ) : `string`
  - openssl_decrypt ( string $data , string $method , string $key [, int $options = 0 [, string $iv = "" [, string $tag = "" [, string $aad = "" ]]]] ) : `string`
    >   openssl 加密方法

## 緩衝區 ##
    主要用途為，當PHP仍然在執行，但有訊息想要先行輸出的時候，可以使用此緩衝區先傳送或處理訊息
  - ob_flush() : `void`
    >   將目前的輸出內容送至緩衝區

  - flush()
    >   無論PHP 目前執行在任何情況，將目前buffer內的內容輸出至瀏覽器

  - ob_implicit_flush ([ int $flag = 1 ] ) : `void`
    >   開啟隱式 flush 模式。開啟模式後，不需要每次都呼叫 flush() 函數輸出內容
    >   1 to turn implicit flushing on, 0 otherwise.

  - ob_get_level ( void ) : `int`
    >   For users confused about getting "1" as a return value from ob_get_level at the beginning of a script: this likely means the PHP ini directive "output_buffering" is not set to off / 0. PHP automatically starts output buffering for all your scripts if this directive is not off (which acts as if you called ob_start on the first line of your script).

  - ob_start ([ callable $output_callback = NULL [, int $chunk_size = 0 [, int $flags = PHP_OUTPUT_HANDLER_STDFLAGS ]]] ) : `bool`
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

## 圖像處理 ##

## Class ZipArchive ##
- PHP 5.2 之後才會支援的 Class，主要用來做zip壓縮相關的操作
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

## 進階應用 ##
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
    // 刪除 html 標記
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

## 引用語法 ##
- include_once
    >   將會引用程式碼一次，若已被引用則不會再次引用

- require_once
    >   與include_once相同，差別為找不到檔案時，將會回傳錯誤，程式終止不運行

- 退出引用程式時，在該被引用的 php 內，使用 `return` 即可回到引用頁

## 匿名函數 ##
- 此為 PHP **5.4** 以後新增之重要功能
  1. 匿名函數（Anonymous functions），也叫閉包函數（closures），允許臨時建立一個沒有指定名稱的函數。最經常用作回調函數 callable 參數值使用。 (Closure class)
  2. 若要使用父區域(parent scope)變數值，必須使用 use()，且 use 內使用的變數值是取該匿名函數被**定義**(defined)的當下取值。
    ```php
    $message = 'hello';

    // No "use"
    $example = function () {
        var_dump($message);
    };
    $example();
    // echo null

    // Inherit $message
    $example = function () use ($message) {
        var_dump($message);
    };
    $example();
    // echo 'hello'

    // Inherited variable's value is from when the function
    // is defined, not when called
    $message = 'world';
    $example();
    // echo 'hello'
    ```
  3. 除非 use 內使用的值為 **call by reference**
    ```php
    $message = 'hello';

    // Inherit by-reference
    $example = function () use (&$message) {
        var_dump($message);
    };
    $example();
    // echo 'hello'

    // The changed value in the parent scope
    // is reflected inside the function call
    $message = 'world';
    $example();
    // echo 'world'
    ```
  4. 匿名函數繼承父區域(parent scope)的變數與全域變數是不同的，閉包的父作用域是定義該閉包的函數（不一定是呼叫它的函數）。
    ```php
    // 官方範例：
    class Cart
    {
        const PRICE_BUTTER  = 1.00;
        const PRICE_MILK    = 3.00;
        const PRICE_EGGS    = 6.95;

        protected   $products = array();

        public function add($product, $quantity)
        {
            $this->products[$product] = $quantity;
        }

        public function getQuantity($product)
        {
            return isset($this->products[$product]) ? $this->products[$product] : FALSE;
        }

        public function getTotal($tax)
        {
            // callback 的父區域
            $total = 0.00;
            /**
             * 匿名函數，這個匿名函數的父作用域就是在這個 getTotal 函數內
             * 所以 該匿名函式使用 use 的 $total 是取自上面的 $total = 0.00;
             */
            $callback =
                function ($quantity, $product) use ($tax, &$total)
                {
                    $pricePerItem = constant(__CLASS__ . "::PRICE_" . strtoupper($product));
                    $total += ($pricePerItem * $quantity) * ($tax + 1.0);
                };

            array_walk($this->products, $callback);
            return round($total, 2);;
        }
    }

    $my_cart = new Cart;

    // 往購物車裡添加項目
    $my_cart->add('butter', 1);
    $my_cart->add('milk', 3);
    $my_cart->add('eggs', 6);

    // 打出出總價格，其中有 5% 的銷售稅.
    print $my_cart->getTotal(0.05) . "\n";
    // 最后結果是 54.29
    ```
- PHP **7.4** 新增箭頭函數 (Arrow Functions)
  1. 箭頭函数是 PHP 7.4 的新語法，是一種更簡潔的 匿名函數 寫法。
  2. 箭號函數的基本語法為 `fn(argument_list) => expr`
  3. 箭號函數支援與 匿名函数 相同的功能，只是其父範圍的變數總是自動的。
    ```php
    $y = 1;
    // $fn1 與 $fn2 作用相同
    $fn1 = fn($x) => $x + $y;
    // 等同 using $y by value:
    $fn2 = function ($x) use ($y) {
        return $x + $y;
    };

    // Arrow functions capture variables by value automatically, even when nested
    $z = 1;
    $fn = fn($x) => fn($y) => $x * $y + $z;
    // Outputs 51
    var_export($fn(5)(10));
    ```

## 寫法邏輯 ##
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


## OOP & 實用class ##
- 靜態方法
  - 該class 不需要先被 new 出一個實體，即可直接使用內部的變量或者方法
  - 對於執行效率而言，靜態的方式執行效率較好

- 物件引用
    兩個冒號（::)是對類中的方法的靜態引用
    與 (->) 比較來說
    也就是不需要實例化對象(不需要new)，直接通過類名對類中的方法進行引用

  - Static 屬性和方法
    1. Class之外：$物件變數->(限方法)、ClassName:: (屬性和方法)
    2. Class當中：self:: & parent:: (屬性和方法)、$this-> (限方法)

  - 非Static 屬性和方法 (實例物件，被 new 出來的實體)
    1. Class之外：$物件變數-> (屬性和方法)
    2. Class當中：self:: & parent:: (限方法)、$this-> (屬性和方法)

- $this vs self
  - $this 是針對有實例化的物件，self:: 是對於靜態未實例化的物件
  - Use $this to refer to the current object. Use self to refer to the current class.
  - In other words, use $this->member for non-static members, use self::$member for static members.

- self vs static

- instanceof 判斷某驗數是否為某個 class 的實體
  - `$db->connection instanceof mysqli`

- Singleton Pattern 單例模式
    >   舉例來說，最常用到的 db 連線，為避免一值 new 出新的實體，而能發揮重用
    >   延續之前的 instance e.g. mysqli::insert_id，若 new 新的實體，便無法抓到先前的 insert_id
  ```php
    class MySQL
    {
        // 實體
        private static $_instance;

        // 連線資源
        public $connection = null;
        private $new_db = null;

        // 用戶端字集 (可指定)
        public $_client_character_set;

        public function __construct($host, $user, $password, $dbname, $charset = 'utf8')
        {
            $this->connection = new mysqli($host, $user, $password, $dbname);

            if ($this->connection->connect_errno) {
                die("Oops! Something went wrong. Please contact CR IT Section.");
            }

            mysqli_report(MYSQLI_REPORT_ERROR | MYSQLI_REPORT_STRICT);
            //mysqli_report(MYSQLI_REPORT_OFF);

            $this->_client_character_set = strtolower($charset);

            // 指定連線用的字集
            $this->connection->set_charset($this->_client_character_set);
            // composer require joshcam/mysqli-database-class
            $this->new_db = new MysqliDb($this->connection);
        }

        public function setCharset($charset)
        {
            $this->_client_character_set = strtolower($charset);
            $this->new_db = $this->new_db->addConnection('default', ['charset' => $this->_client_character_set]);
            $this->connection->set_charset($this->_client_character_set);
        }

        // 若使用到實體呼叫，便要用 MySQL::connect 此 function 會回傳一實體
        public static function connect($host, $user, $password, $dbname, $charset = 'utf8')
        {
            if (!(self::$_instance instanceof MySQL)) {
                $class_name = __CLASS__;
                self::$_instance = new $class_name($host, $user, $password, $dbname, $charset);
            }
            return self::$_instance;
        }
    }
  ```

- namespace 命名空間
  - 若定義了 namespace，代表底下定義的 class 或 new 的 class 都會以該空間為主
  - 使用別的 namespace 需要使用 use
  - global namespace 則是這樣用
    ```php
    $exmaple = new \globalClass();
    ```

- ReflectionClass 反射類別
    >   可以得到Class的各種屬性，例如
    ```php
    $mysqlic = new ReflectionClass('mysqli');
    // 得到 mysqli 類別的複製
    $mysqli = $mysqlic->newInstanceArgs($params);
    ```

- Image Processing (ImageMagick) 圖像處理

## CLass ##
定義 class 方法，使用 new Class() 實作出 object

1. 抽象方法
    在類中，沒有方法體的方法就是抽象方法。
    ```php
    abstract 可見性 function 方法名稱(參數1, ..);      // 如果沒有察看地指定可見性，則預設為public
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

3. 抽象類的特點：
   - 抽象類無法實例化，只能被繼承。
   - 抽象類不一定有抽象方法，有抽象方法的類，一定是抽象類。
   - 抽象方法的可見性無法是private
   - 抽象方法在子類中，需要重寫。

4. 什麼時候需要用抽象類？
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

5. 介面

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

6. 抽象類和介面的區別
   - 介面是一種特殊的抽象類，介面中只包含抽象方法，沒有成員屬性。
   - 類實現（implements）介面時，必須完全實現介面中的所有方法；
   - 類繼承（extends）抽象類時，只需對需要用到的抽象方法進行重寫。
   - 抽象類只能單繼承，但介面卻是多繼承，類對介面的實現也是多實現。


## 資料庫操作 ##
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


## 安全性寫法 ##
- 使用超全域變數時 ($GLOBALS, $_POST, $_GET 等等)，必須使用htmlentities()，避免特殊字元，令駭客找出漏洞

- 過濾參數
  - filter_var ( mixed $variable [, int $filter = FILTER_DEFAULT [, mixed $options ]] ) : `mixed`
    >   可以拿來過濾一些使用者輸入資料
    >   $variable 基本上放字串， $filter 放以下的過濾ID， $options 可以額外增加選項，若是在用 flags
    1. FILTER_SANITIZE_STRING - 可額外濾除特殊符號
    2. FILTER_SANITIZE_FULL_SPECIAL_CHARS - 與 call **htmlspecialchars($字串 , ENT_QUOTES)** 相同
    3. FILTER_SANITIZE_EMAIL - 移除數字與英文還有以下特殊符號 !#$%&'*+-=?^_`{|}~@.[] 以外之文字

  - filter_var_array ( array $data [, mixed $definition [, bool $add_empty = TRUE ]] ) : mixed
    >   可以針對陣列內不同的資料做不同的過濾， $definition 可以對應輸入資料位置(但不方便，必須每個欄位都對應)
    >   $add_empty 會將 null 塞入不存在的陣列直
    >   若需要使用 filter flags，可使用以下
    >   `array_walk_recursive($para, fn(&$val, $key) => $val = filter_var($val, FILTER_SANITIZE_FULL_SPECIAL_CHARS, FILTER_FLAG_NO_ENCODE_QUOTES));`

  - filter_input

- 檔案驗證 MIME
  - finfo_open

- 使用 include php 網頁時相關技巧；
    >   可以將需要被include的網頁放置在document root之外的資料夾內
    >   再使用
        ```php
        include __DIR__ . '/../src/includeFile.php';
        ```
    >   因為user不能使用'your.site/../src/includeFile.php'去瀏覽你的頁面

- 雜湊演算法
  - hash_algos ( ) : `array`
    >   列出目前所支援的雜湊演算方式
  - hash ( string $algo , string $data [, bool $binary = FALSE ] ) : `string`|false
    >   產出雜湊值

- 密碼驗證
  - password_hash ( string $password , int $algo [, array $options ] ) : `string`
    >   最好的資料長度建議為255
    >   預設的方式將會動態產生 salt，此值是動態加入以擾亂辨識原本的密碼型態 (避免有人使用先算好的雜湊值碰撞硬解)

    算法 ($algo)
    -  PASSWORD_DEFAULT 使用 bcrypt 算法 (PHP 5.5.0 預設)。 注意，該常數會隨着 PHP 加入更新更高強度的算法而改變。 所以，使用此常數生成結果的長度將在未來有變化。 因此，資料程式庫裡儲存結果的列可超過60個字元（最好是255個字元）。
    -  PASSWORD_BCRYPT 使用 CRYPT_BLOWFISH 算法建立雜湊。這會產生相容使用 "$2y$" 的 crypt()。 結果將會是 60 個字元的字串， 或者在失敗時傳回 FALSE。

  - password_verify ( string $password , string $hash ) : `boolean`
    >   可以驗證經由 password_hash 產生出來的亂數



## 系統紀錄參考 ##
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


## PHP 相關套件語法使用 ##

### TCPDF ###
```php
//Cell($w, $h=0, $txt='', $border=0, $ln=0, $align='', $fill=0, $link='', $stretch=0, $ignore_min_height=false, $calign='T', $valign='M')

```

### PHP WORD ###

### PHP SPREADSHEET ###
```php
// 自動調整欄位的寬度(但是是以英文字母寬度為主，$column 是數字)
$spreadsheet->getActiveSheet()->getColumnDimension(Cell\Coordinate::stringFromColumnIndex($column))->setAutoSize(true);
```

## PHP 棄用函式以及取代方案 ##
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
    >   `str_split()` 利用字串長度分割為陣列

- preg_replace ()
    >   The /e modifier is deprecated, use preg_replace_callback instead
    >   主要為使用 /正規表示式/e 被棄用，被選取項目應該用 preg_replace_callback 來對被選取的字串做進階處理

**PHP 7.2棄用**
- each ( array &$array ) : `array`
    >   傳回陣列中目前的鍵／值對，並且將陣列指標向前移動一步
    >   在執行 each() 之後，陣列指標將停留在陣列中的下一個單元或者當碰到陣列結尾時停留在最后一個單元。如果要再用 each 重新遍歷陣列，必須使用 reset()
    >   **return** 鍵值對被傳回為四個單元的陣列，鍵名為0，1，key和 value。單元 0 和 key 包含有陣列單元的鍵名，1 和 value 包含有資料。
    >   常配合 list() 用為取鍵值對的方法，

## PHP 新方法與新用法 ##
**PHP 7.4後支援**
```php
$array['key'] ??= computeDefault();
// is roughly equivalent to
if (!isset($array['key'])) {
    $array['key'] = computeDefault();
}
```

## PHP 套件管理 composer ##
- composer 新增套件
  - `composer require [package_name]`
