### url (معالجة الروابط في Node.js)
**الوصف**:
موديول url يوفر أدوات لتحليل، بناء، وتنسيق الروابط (URLs) في Node.js. يدعم معيار WHATWG الحديث وواجهة API قديمة (Legacy). يُستخدم بكثرة في تطبيقات الويب، التعامل مع REST APIs، معالجة الروابط الديناميكية، وتحويل المسارات.

---
#### أهم الكلاسات والدوال:

```js
// استيراد الموديول
const url = require('node:url');

// WHATWG URL API (الموصى بها)
const myURL = new URL('https://user:pass@nodejs.org:8080/docs?x=1#top');
console.log(myURL.hostname); // nodejs.org
console.log(myURL.searchParams.get('x')); // 1

// Legacy API (قديمة)
const parsed = url.parse('https://nodejs.org/docs?x=1#top');
console.log(parsed.hostname); // nodejs.org

// تنسيق كائن URL إلى نص
console.log(url.format(myURL));

// تحويل مسار إلى رابط file://
console.log(url.pathToFileURL('/tmp/test.txt').href);

// تحويل رابط file:// إلى مسار
console.log(url.fileURLToPath('file:///tmp/test.txt'));

// domainToASCII/domainToUnicode: دعم IDN
console.log(url.domainToASCII('مثال.إختبار'));
console.log(url.domainToUnicode('xn--mgbh0fb.xn--kgbechtv'));

// URLSearchParams: إدارة بارامترات الاستعلام
const params = new URLSearchParams('a=1&b=2');
params.append('c', '3');
console.log(params.toString()); // a=1&b=2&c=3
```

---
#### شرح البارامترات والقيم:
- **input**: نص الرابط (String).
- **base**: رابط أساسي (اختياري) لحل الروابط النسبية.
- **urlObject**: كائن URL أو كائن قديم (Legacy).
- **options**: خيارات إضافية لبعض الدوال.
- **name/value**: اسم وقيمة بارامتر الاستعلام.

---
#### أمثلة عملية:

**1. استخراج معلومات من رابط:**
```js
const { URL } = require('node:url');
const u = new URL('https://example.com:8080/path?foo=bar#hash');
console.log(u.protocol); // https:
console.log(u.host);     // example.com:8080
console.log(u.pathname); // /path
console.log(u.search);   // ?foo=bar
console.log(u.hash);     // #hash
```

**2. بناء رابط ديناميكي:**
```js
const u = new URL('https://api.example.com/');
u.pathname = '/v1/users';
u.searchParams.set('id', 123);
console.log(u.toString()); // https://api.example.com/v1/users?id=123
```

**3. التعامل مع بارامترات الاستعلام:**
```js
const params = new URLSearchParams('a=1&b=2');
params.append('c', '3');
params.delete('a');
console.log(params.get('b')); // 2
console.log(params.toString()); // b=2&c=3
```

**4. تحويل مسار إلى رابط والعكس:**
```js
const { pathToFileURL, fileURLToPath } = require('node:url');
const fileUrl = pathToFileURL('/tmp/test.txt');
console.log(fileUrl.href); // file:///tmp/test.txt
console.log(fileURLToPath(fileUrl)); // /tmp/test.txt
```

---
#### أخطاء شائعة وحلولها:
- **استخدام API قديمة (url.parse, url.format) مع روابط حديثة:** قد ينتج سلوك غير متوقع مع Unicode أو روابط غير قياسية. الحل: استخدم WHATWG URL API دائمًا.
- **نسيان تحديد base عند تحليل روابط نسبية:** يؤدي لخطأ. الحل: مرر base عند استخدام new URL مع روابط نسبية.
- **نسيان ترميز القيم يدويًا:** WHATWG API تتكفل بالترميز تلقائيًا، لكن مع Legacy API يجب الانتباه.
- **تمرير كائن غير نصي إلى URL:** يجب تحويله إلى نص أولاً.

---
#### نصائح احترافية:
- **استخدم WHATWG URL API دائمًا** (new URL, URLSearchParams) فهي أكثر أمانًا ودقة وتدعم Unicode بشكل أفضل.
- **تجنب استخدام url.parse وurl.format** إلا للتوافق مع كود قديم.
- **استخدم URLSearchParams لإدارة بارامترات الاستعلام** بدل التلاعب اليدوي بالنصوص.
- **عند التعامل مع روابط ملفات محلية**، استخدم pathToFileURL وfileURLToPath لضمان التوافق بين الأنظمة.
- **تحقق من صحة الروابط قبل استخدامها** في العمليات الحساسة (تنزيل، رفع، إلخ).

---
#### بدائل حديثة:
- **مكتبة whatwg-url**: نفس معيار WHATWG وتستخدم داخليًا في Node.js.
- **مكتبة qs**: لإدارة بارامترات الاستعلام بشكل متقدم في تطبيقات الويب.

---
#### ملاحظات تقنية:
- **WHATWG URL API** هي الافتراضية في Node.js منذ الإصدار 10+.
- **دعم Unicode وIDN** أفضل بكثير في WHATWG API.
- **الترميز التلقائي** للقيم في URLSearchParams يمنع أخطاء شائعة في الروابط.
- **التوافق**: معظم مكتبات الويب الحديثة تعتمد WHATWG URL API.

---
#### مصادر:
- [توثيق Node.js الرسمي - url](https://nodejs.org/docs/latest/api/url.html) 