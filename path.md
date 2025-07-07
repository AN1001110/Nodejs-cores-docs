### path (معالجة المسارات في Node.js)

**الوصف**:
موديول path يوفر أدوات للتعامل مع مسارات الملفات والمجلدات بشكل متوافق مع جميع أنظمة التشغيل (Windows, POSIX). يُستخدم في بناء تطبيقات تتعامل مع الملفات، توليد المسارات الديناميكية، والتحقق من صحة المسارات.

---

#### أهم الدوال والخصائص:

```js
const path = require("node:path");

// basename: اسم الملف بدون المسار
console.log(path.basename("/tmp/file.txt")); // file.txt

// dirname: اسم المجلد الحاوي
console.log(path.dirname("/tmp/file.txt")); // /tmp

// extname: امتداد الملف
console.log(path.extname("index.html")); // .html

// join: دمج عدة أجزاء لمسار واحد
console.log(path.join("/tmp", "folder", "file.txt")); // /tmp/folder/file.txt

// resolve: تحويل أجزاء لمسار مطلق
console.log(path.resolve("folder", "file.txt")); // /مسار/حالي/folder/file.txt

// isAbsolute: هل المسار مطلق؟
console.log(path.isAbsolute("/tmp/file.txt")); // true

// normalize: تصحيح المسار
console.log(path.normalize("/tmp//folder/../file.txt")); // /tmp/file.txt

// parse: تحليل المسار لكائن
console.log(path.parse("/tmp/file.txt"));
// { root: '/', dir: '/tmp', base: 'file.txt', ext: '.txt', name: 'file' }

// format: بناء مسار من كائن
console.log(path.format({ dir: "/tmp", name: "file", ext: ".txt" })); // /tmp/file.txt

// relative: حساب المسار النسبي بين مسارين
console.log(path.relative("/data", "/data/app/file.js")); // app/file.js

// sep: فاصل المسارات (\ أو /)
console.log(path.sep); // على ويندوز: \\، على لينكس: /

// delimiter: فاصل متغيرات البيئة
console.log(path.delimiter); // ; على ويندوز، : على لينكس

// win32/posix: دوال خاصة بكل نظام
console.log(path.win32.basename("C:\\temp\\myfile.html")); // myfile.html
console.log(path.posix.basename("/tmp/myfile.html")); // myfile.html
```

---

#### شرح البارامترات والقيم:

- **path**: نص يمثل المسار (String).
- **...paths**: أجزاء متعددة لمسار.
- **suffix**: لاحقة اختيارية لإزالتها من اسم الملف.
- **pathObject**: كائن يحتوي على خصائص المسار (dir, base, ext, name).

---

#### أمثلة عملية:

**1. توليد مسار ملف في مجلد مؤقت:**

```js
const tmpFile = path.join("/tmp", `file_${Date.now()}.txt`);
console.log(tmpFile);
```

**2. استخراج اسم الملف وامتداده:**

```js
const file = "/var/www/index.html";
console.log(path.basename(file)); // index.html
console.log(path.extname(file)); // .html
```

**3. بناء مسار متوافق مع النظام:**

```js
const uploadDir = path.join(__dirname, "uploads", "images");
console.log(uploadDir);
```

**4. التحقق من المسار المطلق:**

```js
if (!path.isAbsolute(process.argv[2])) {
  console.error("يرجى إدخال مسار مطلق!");
}
```

**5. دعم Windows/POSIX:**

```js
// نتائج basename تختلف حسب النظام
console.log(path.basename("C:\\temp\\myfile.html")); // على ويندوز: myfile.html، على لينكس: C:\temp\myfile.html
// استخدم path.win32 أو path.posix للثبات
```

---

#### أخطاء شائعة وحلولها:

- **نسيان استخدام join/resolve:** دمج المسارات يدويًا يسبب أخطاء في الفواصل. الحل: استخدم دائمًا join أو resolve.
- **الاعتماد على فاصل المسارات الثابت:** استخدم path.sep بدل / أو \\ مباشرةً.
- **نسيان normalize:** قد يؤدي لمسارات غير صحيحة عند وجود .. أو // متكررة. الحل: استخدم normalize.
- **عدم التحقق من النظام عند التعامل مع مسارات خارجية:** استخدم win32/posix عند الحاجة للثبات عبر الأنظمة.

---

#### نصائح احترافية:

- **استخدم join وresolve دائمًا** لبناء المسارات بدل التلاعب اليدوي بالنصوص.
- **استعمل parse وformat** لتحليل أو بناء مسارات معقدة.
- **استخدم path.sep وpath.delimiter** عند التعامل مع متغيرات البيئة أو المسارات الديناميكية.
- **عند التعامل مع مسارات من مصادر خارجية** (مستخدم، API)، تحقق من صحتها لتجنب الثغرات.
- **استخدم path.win32 أو path.posix** إذا كنت تحتاج نتائج ثابتة بغض النظر عن النظام.

---

#### ملاحظات تقنية:

- **الفروقات بين Windows وPOSIX:** بعض الدوال تعطي نتائج مختلفة حسب النظام. استخدم win32/posix للثبات.
- **المسارات المطلقة والنسبية:** resolve يحول أي مسار إلى مطلق بناءً على المسار الحالي.
- **الأمان:** لا تثق في المسارات القادمة من المستخدمين، تحقق منها قبل استخدامها في عمليات حساسة.

---

#### مصادر:

- [توثيق Node.js الرسمي - path](https://nodejs.org/docs/latest/api/path.html)
