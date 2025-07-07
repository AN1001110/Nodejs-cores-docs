# url (معالجة الروابط في Node.js)

## الوصف
موديول url يوفر أدوات لتحليل، بناء، وتنسيق الروابط (URLs) في Node.js. يدعم معيار WHATWG الحديث وواجهة API قديمة (Legacy). يُستخدم بكثرة في تطبيقات الويب، التعامل مع REST APIs، معالجة الروابط الديناميكية، وتحويل المسارات.

---

## فهرس الكلاسات والدوال
| الكلاس/الدالة | الوصف |
|---------------|-------|
| [`URL`](#url) | كلاس WHATWG URL الحديث |
| [`URLSearchParams`](#urlsearchparams) | إدارة بارامترات الاستعلام |
| [`url.parse`](#urlparseurlstring-parsequerystring-slashesdenotehost) | تحليل رابط (قديم) |
| [`url.format`](#urlformaturlobject-options) | تنسيق كائن URL إلى نص (قديم) |
| [`url.pathToFileURL`](#urlpathtofileurlpath) | تحويل مسار إلى رابط file:// |
| [`url.fileURLToPath`](#urlfileurltopathurl) | تحويل رابط file:// إلى مسار |
| [`url.domainToASCII`](#urldomaintoasciidomain) | تحويل دومين Unicode إلى ASCII |
| [`url.domainToUnicode`](#urldomaintounicodedomain) | تحويل دومين ASCII إلى Unicode |

---

## شرح الكلاسات والدوال الأساسية

### URL(input[, base])
- **input**: نص الرابط (String)
- **base**: رابط أساسي (اختياري) لحل الروابط النسبية
- **الوصف**: كلاس WHATWG الحديث لتحليل وبناء الروابط.
- **مثال:**
```js
const myURL = new URL('https://user:pass@nodejs.org:8080/docs?x=1#top');
console.log(myURL.hostname); // nodejs.org
console.log(myURL.searchParams.get('x')); // 1
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/url.html#class-url)

---

### URLSearchParams(init)
- **init**: نص أو كائن أو مصفوفة بارامترات الاستعلام
- **الوصف**: إدارة بارامترات الاستعلام بسهولة وأمان.
- **مثال:**
```js
const params = new URLSearchParams('a=1&b=2');
params.append('c', '3');
console.log(params.toString()); // a=1&b=2&c=3
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/url.html#class-urlsearchparams)

---

### url.parse(urlString[, parseQueryString, slashesDenoteHost])
- **urlString**: نص الرابط
- **parseQueryString**: هل يتم تحليل بارامترات الاستعلام؟ (اختياري)
- **slashesDenoteHost**: هل // تعني بداية المضيف؟ (اختياري)
- **الوصف**: تحليل رابط إلى كائن (واجهة قديمة - Legacy).
- **مثال:**
```js
const url = require('node:url');
const parsed = url.parse('https://nodejs.org/docs?x=1#top');
console.log(parsed.hostname); // nodejs.org
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/url.html#url_url_parse_urlstring_parsequerystring_slashesdenotehost)

---

### url.format(urlObject[, options])
- **urlObject**: كائن URL أو كائن قديم (Legacy)
- **options**: خيارات إضافية (اختياري)
- **الوصف**: تنسيق كائن URL إلى نص (واجهة قديمة - Legacy).
- **مثال:**
```js
const url = require('node:url');
const myURL = new URL('https://nodejs.org/docs?x=1#top');
console.log(url.format(myURL));
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/url.html#url_url_format_urlobject_options)

---

### url.pathToFileURL(path)
- **path**: مسار الملف (String)
- **الوصف**: تحويل مسار إلى رابط file://
- **مثال:**
```js
const { pathToFileURL } = require('node:url');
console.log(pathToFileURL('/tmp/test.txt').href); // file:///tmp/test.txt
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/url.html#url_url_pathtofileurl_path)

---

### url.fileURLToPath(url)
- **url**: رابط file:// أو كائن URL
- **الوصف**: تحويل رابط file:// إلى مسار نظام الملفات.
- **مثال:**
```js
const { fileURLToPath, pathToFileURL } = require('node:url');
const fileUrl = pathToFileURL('/tmp/test.txt');
console.log(fileURLToPath(fileUrl)); // /tmp/test.txt
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/url.html#url_url_fileurltopath_url)

---

### url.domainToASCII(domain)
- **domain**: نص الدومين (قد يحتوي Unicode)
- **الوصف**: تحويل دومين Unicode إلى ASCII (IDN)
- **مثال:**
```js
const { domainToASCII } = require('node:url');
console.log(domainToASCII('مثال.إختبار'));
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/url.html#url_url_domaintoascii_domain)

---

### url.domainToUnicode(domain)
- **domain**: نص الدومين (ASCII)
- **الوصف**: تحويل دومين ASCII إلى Unicode
- **مثال:**
```js
const { domainToUnicode } = require('node:url');
console.log(domainToUnicode('xn--mgbh0fb.xn--kgbechtv'));
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/url.html#url_url_domaintounicode_domain)

---

## حالات الاستخدام الشائعة
- استخراج معلومات من رابط (protocol, host, pathname, search, hash)
- بناء روابط ديناميكية في تطبيقات REST APIs
- إدارة بارامترات الاستعلام بسهولة
- تحويل المسارات المحلية إلى روابط والعكس
- دعم الروابط الدولية (IDN/Unicode)

---

## أفضل الممارسات
- استخدم WHATWG URL API دائمًا (new URL, URLSearchParams)
- تجنب استخدام url.parse وurl.format إلا للتوافق مع كود قديم
- استخدم URLSearchParams لإدارة بارامترات الاستعلام
- استخدم pathToFileURL وfileURLToPath مع روابط الملفات المحلية
- تحقق من صحة الروابط قبل استخدامها في العمليات الحساسة

---

## التحذيرات الأمنية
- لا تثق في الروابط القادمة من المستخدمين، تحقق منها قبل استخدامها
- انتبه للترميز التلقائي في WHATWG API (قد يغير شكل الرابط)

---

## أدوات التصحيح المتعلقة
- [node --inspect](https://nodejs.org/en/docs/guides/debugging-getting-started/)
- [valid-url](https://www.npmjs.com/package/valid-url) (للتحقق من صحة الروابط)

---

## اختبار تفاعلي
```js
const test = require('node:test');
const assert = require('node:assert');
const { URL } = require('node:url');

test('اختبار URLSearchParams', () => {
  const params = new URLSearchParams('a=1&b=2');
  params.append('c', '3');
  assert.strictEqual(params.toString(), 'a=1&b=2&c=3');
});
```

---

## نصائح الخبراء
- استخدم WHATWG API دائمًا لدعم Unicode والأمان
- استخدم URLSearchParams بدل التلاعب اليدوي بالنصوص
- تحقق من base عند تحليل روابط نسبية

---

## ملاحظات تقنية
- WHATWG URL API هي الافتراضية في Node.js منذ الإصدار 10+
- الترميز التلقائي في URLSearchParams يمنع أخطاء شائعة
- راجع [توثيق Node.js الرسمي - url](https://nodejs.org/docs/latest/api/url.html) لأي تحديثات 