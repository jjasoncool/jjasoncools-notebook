
基本語法
---
- 搜尋字串
    String word = "cat";
    String text = "The cat is on the table";
    Boolean found;
    found = text.contains(word);



JDBC 與 Hibernate
---
- JDBC部分
    與資料庫連線 close 順序：
        finally {
            //關閉 ResultSet
            if (rs != null) {
                rs.close();
            }
            //關閉 PreparedStatement
            if (stmt != null) {
                stmt.close();
            }
            //關閉 Connection
            if (con != null)
                con.close();
        }
- Hibernate


servlet
---
request.getContextPath()
    可以取得主機名稱後面的專案名稱，也就是contextRoot

request.getServerName()
    如其名，取得主機名稱

request.getServerPort()
    如其名，取得主機port號碼

MVC 架構
---
使用spring框架，必須先從XML設定 指定各種行為的對應模式
1.  web.xml 可以指定網址後面的post 指定哪個class 執行
如下例：當URL變為/helloWorld.do 時，指定執行com.javapapers.sample.ajax.HelloWorld
```xml
    <servlet>
        <servlet-name>helloWorld</servlet-name>
        <servlet-class>com.javapapers.sample.ajax.HelloWorld</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>helloWorld</servlet-name>
        <url-pattern>/helloWorld.do</url-pattern>
    </servlet-mapping>
```

2.  MVC架構下的 web.xml 與 後續自訂的 mvc-config.xml 設定
如下例：
```xml
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>
            org.springframework.web.servlet.DispatcherServlet
        </servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>/WEB-INF/mvc-config.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>*.do</url-pattern>
    </servlet-mapping>
```

3.  mvc-config.xml 內
```xml
    <bean id="這個bean的名稱" class="class路徑">
        <property name="drawingSessionFactory">
            <ref bean="另一個bean的名稱"/>
        </property>
    </bean>
```

自訂EL架構
---
如果你設計了一個 Util 類別，當中有個 length() 靜態方法可以將所傳入的 Collection 長度傳回。例如原先可能這麼使用它：

`<%= Util.length(reqeust.getAttribute("someList")) %>`

如果 someList 實際上是個 List 介面實作，而其長度為 10，這會傳回結果 10。

如果函式的部份也可以使用 EL 來呼叫，以下也許是你想要的撰寫方式：

`${ utilname:length(requestScope.someList)  }`

這樣的寫法著實簡潔許多，如果這是你想要的需求，則可以自訂 EL 函式來滿足這項需求。自訂 EL 函式的第一步是撰寫類別，它必須是個公開（public）類別，而想要呼叫的方法必須是公開且為靜態方法。例如你的 Util 類別可能是這麼撰寫的：
```java
package cc.openhome;

import java.util.Collection;

public class Util {
    public static int length(Collection collection) {
        return collection.size();
    }
}
```
Web 容器必須知道如何將這個類別中的 length() 方法當作 EL 函式來使用，所以你必須撰寫一個標籤程式庫描述檔（Tag Library Descriptor, TLD）檔案，這個檔案是個 XML 文件，副檔名為 *.tld。例如：

openhome.tld
```xml
<?xml version="1.0" encoding="UTF-8"?>
<taglib version="2.1" xmlns="http://java.sun.com/xml/ns/javaee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
    http://java.sun.com/xml/ns/javaee/web-jsptaglibrary_2_1.xsd">
  <tlib-version>1.0</tlib-version>
  <short-name>openhome</short-name>
  <uri>https://openhome.cc/util</uri>
  <function>
    <description>Collection Length</description>
    <name>length</name>
    <function-class>
        cc.openhome.Util
    </function-class>
    <function-signature>
        int length(java.util.Collection)
    </function-signature>
  </function>
</taglib>
```
在 TLD 檔案中，重要的部份已在程式碼中直接標示。${util.length(...)} 的例子中，length 名稱就對應於 <name> 標籤的設定，而實際上 length 名稱背後執行的類別與真正的靜態方法，則分別由 <function-class> 與 <function-signature> 來設定。至於 <uri> 標籤則是在 JSP 網頁中會使用到，稍後就會了解其作用。

你可以將這個 TLD 檔案直接放在 WEB-INF 資料夾下，
如此容器會自動找到 TLD 檔案並載入。如果要放在JAR檔案中，
設定的方式之後還會作說明。
補充：若放在某資料夾內，可透過web.xml 做設定
```jsp
<jsp-config>
    <taglib>
        <taglib-uri>/cr/util</taglib-uri>
        <taglib-location>/WEB-INF/cr_util.tld</taglib-location>
    </taglib>
</jsp-config>
```

在這邊為了簡化，先將 TLD 檔案放在 WEB-INF 資料夾下。接著可以撰寫一個 JSP 來使用這個自訂 EL 函式。例如：
```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<%@taglib prefix="utilname" uri="https://openhome.cc/util"%>
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>自訂EL函式</title>
    </head>
    <body>
        ${ util:length(requestScope.someList) }
    </body>
</html>
```
在這邊使用 taglib 指示元素告訴容器，在轉譯這個 JSP 時，
會用到對應 uri 屬性的自訂 EL 函式，容器會尋找讀入的 TLD 中，<uri> 標籤設定中有對應 uri 屬性的名稱
這就是剛才在 openhome.tld 中定義 <uri> 標籤的目的。
至於 prefix 屬性則是設定前置名稱，如此若 JSP 中有多個來自不同設計者的 EL 自訂函式時
就可以避免名稱衝突的問題，所以要使用這個自訂 EL 函式時，就可以用 ${utilname:length(...)} 的方式。
