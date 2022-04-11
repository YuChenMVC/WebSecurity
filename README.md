# https://yuchenmvc.github.io/WebSecurity/ 網頁版

## SQL Injection

1. `使用參數化查詢`
```C#
command.Parameters.AddWithValue("@name", Request.Query["name"].ToString());
```

2. `使用LINQ`

當執行LINQ query時, 到SQL Server會轉成參數查詢

3. `使用內建.NET Core`
```C#
[Route("Home/About/{id}")]
```
如果 {id} 為 int 型別,
<br>
那麼攻擊者試圖加奇怪的字串, 不會被解析為 int, 以防止資料庫注入攻擊

4. `盡可能的最低權限`

注入攻擊可能執行刪除TABLE等指令, 但網站需要刪除表格嗎, 可能很少,
<br>
提供盡可能的低權限至少使攻擊指令無法執行
<br>

## Cross-Site Request Forgery, CSRF

`以.NET Core為例`
<br>
使用anti-forgery token能防止CSRF攻擊,
<br>
每個POST會分2個不同的值到Server驗證, 確保為合法
<br>
先註冊Service
```C#
builder.Services.AddAntiforgery(options =>
{
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```
還有Controller
```C#
[AutoValidateAntiforgeryToken]
```
Razor Pages也要加
```cshtml
<form method="post">
    @Html.AntiForgeryToken() 
</form>
```
<br>

## Cross-Site Scripting, XSS

攻擊方式

瀏覽器不會判別腳本語言是否為惡意, 藉以注入惡意的JavaScript或HTML payload

>這樣好像沒什麼

```javascript
<script>alert('OAO')</script>
```

>如果這樣 使用者知道被攻擊了

```C#
<style> *{background:#00FF00;}</style>
```

>還有這樣 不僅彈出視窗 連cookie也噴出來了

```javascript
<script>alert(document.cookie)</script>
```

<br>

1. `使用內建.NET Core`

.NET Core編碼器可以透過注入設定
```C#
public class HomeController : Controller
{
    HtmlEncoder _htmlEncoder;
    JavaScriptEncoder _javaScriptEncoder;
    UrlEncoder _urlEncoder;

    public HomeController(HtmlEncoder htmlEncoder,
                          JavaScriptEncoder javascriptEncoder,
                          UrlEncoder urlEncoder)
    {
        _htmlEncoder = htmlEncoder;
        _javaScriptEncoder = javascriptEncoder;
        _urlEncoder = urlEncoder;
    }
}
```
編碼方法
```C#
var js = _javaScriptEncoder.Encode(字串);
```
2. `限制存取`

```C#
builder.Services.ConfigureApplicationCookie(options =>
{
    // 限制Cookie只能由HTTP存取
    options.Cookie.HttpOnly = true;
});
```
3. `編碼`

HTML編碼
```html
&    &amp;
<    &lt;
>    &gt;
"    &quot;
'    &#x27;
```
以及JavaScript, Css, URL編碼

<br>

<!--

## 不安全的直接存取物件 (Insecure Direct Object References, IDOR)

<br>

## 反射型跨站腳本攻擊 (Reflected Cross-Site Scripting)

<br>

## 資訊洩漏 (Information Leakage)

<br>

## 指令注入攻擊 (Command Injection)

<br>

## HTTP Header 注入 (HTTP Header Injection)

<br>

## 未驗證的 URL 轉址 (Unvalidated Redirects and Forwards)

<br>

## 基於 Flash 的 XSS (Flash Cross-Site Scripting)

<br>

## 任意檔案下載 (Arbitrary File Download)

<br>

## 預存式跨站腳本攻擊 (Stored Cross-Site Scripting)

<br>

## 使用已知含漏洞之元件 (Using Known Vulnerable Components)

<br>

## 權限提升 (Privilege Escalation)

<br>

## 弱密碼 (Weak Passwords)

<br>

## 本地檔案引入 (Local File Inclusion, LFI)

<br>

## Server-Side Request Forgery (SSRF)

<br>

## 遠端檔案引入 (Remote File Inclusion)

<br>

## 基於 DOM 的 XSS (DOM-based Cross-Site Scripting)

<br>

## 任意檔案上傳 (Arbitrary File Upload)

<br>

## 邏輯漏洞 (Logic Flaws)

<br>

## 程式碼執行 (Code Execution)

<br>

## 遠端命令執行 (Remote Code Execution)

<br>

## 存取控制缺陷 (Broken Access Control)

<br>

## XML 外部實體注入 (XML External Entities (XXE))

<br>


-->



