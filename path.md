# path (معالجة المسارات في Node.js)

## الوصف

يوفر موديول path أدوات قوية للتعامل مع مسارات الملفات والمجلدات بشكل متوافق مع جميع أنظمة التشغيل (Windows, POSIX). يُستخدم في بناء تطبيقات تتعامل مع الملفات، توليد المسارات الديناميكية، والتحقق من صحة المسارات.

---

## فهرس الدوال

| الدالة                                                  | الوصف                  |
| ------------------------------------------------------- | ---------------------- |
| [`path.basename`](#pathbasenamepath-suffix)             | اسم الملف بدون المسار  |
| [`path.dirname`](#pathdirnamepath)                      | اسم المجلد الحاوي      |
| [`path.extname`](#pathextnamepath)                      | امتداد الملف           |
| [`path.join`](#pathjoinpaths)                           | دمج أجزاء لمسار        |
| [`path.resolve`](#pathresolvepaths)                     | تحويل أجزاء لمسار مطلق |
| [`path.isAbsolute`](#pathisabsolutepath)                | هل المسار مطلق؟        |
| [`path.normalize`](#pathnormalizepath)                  | تصحيح المسار           |
| [`path.parse`](#pathparsepath)                          | تحليل المسار لكائن     |
| [`path.format`](#pathformatpathobject)                  | بناء مسار من كائن      |
| [`path.relative`](#pathrelativerfrom-to)                | حساب المسار النسبي     |
| [`path.sep`](#pathsep)                                  | فاصل المسارات          |
| [`path.delimiter`](#pathdelimiter)                      | فاصل متغيرات البيئة    |
| [`path.win32`](#pathwin32) / [`path.posix`](#pathposix) | دوال خاصة بكل نظام     |

---

## شرح الدوال الأساسية

### path.basename(path[, suffix])

- **path**: نص يمثل المسار (String)
- **suffix**: لاحقة اختيارية لإزالتها من اسم الملف
- **الوصف**: يعيد اسم الملف فقط من المسار.
- **مثال:**

```js
console.log(path.basename("/tmp/file.txt")); // file.txt
```

[توثيق رسمي](https://nodejs.org/docs/latest/api/path.html#path_path_basename_path_ext)

---

### path.dirname(path)

- **path**: نص يمثل المسار
- **الوصف**: يعيد اسم المجلد الحاوي للمسار.
- **مثال:**

```js
console.log(path.dirname("/tmp/file.txt")); // /tmp
```

[توثيق رسمي](https://nodejs.org/docs/latest/api/path.html#path_path_dirname_path)

---

### path.extname(path)

- **path**: نص يمثل المسار
- **الوصف**: يعيد امتداد الملف (مع النقطة).
- **مثال:**

```js
console.log(path.extname("index.html")); // .html
```

[توثيق رسمي](https://nodejs.org/docs/latest/api/path.html#path_path_extname_path)

---

### path.join(...paths)

- **...paths**: أجزاء متعددة لمسار
- **الوصف**: يدمج عدة أجزاء لمسار واحد متوافق مع النظام.
- **مثال:**

```js
console.log(path.join("/tmp", "folder", "file.txt")); // /tmp/folder/file.txt
```

[توثيق رسمي](https://nodejs.org/docs/latest/api/path.html#path_path_join_paths)

---

### path.resolve(...paths)

- **...paths**: أجزاء متعددة لمسار
- **الوصف**: يحول الأجزاء إلى مسار مطلق بناءً على المسار الحالي.
- **مثال:**

```js
console.log(path.resolve("folder", "file.txt"));
```

[توثيق رسمي](https://nodejs.org/docs/latest/api/path.html#path_path_resolve_paths)

---

### path.isAbsolute(path)

- **path**: نص يمثل المسار
- **الوصف**: هل المسار مطلق؟
- **مثال:**

```js
console.log(path.isAbsolute("/tmp/file.txt")); // true
```

[توثيق رسمي](https://nodejs.org/docs/latest/api/path.html#path_path_isabsolute_path)

---

### path.normalize(path)

- **path**: نص يمثل المسار
- **الوصف**: يصحح المسار (يزيل الفواصل الزائدة، يعالج ..)
- **مثال:**

```js
console.log(path.normalize("/tmp//folder/../file.txt")); // /tmp/file.txt
```

[توثيق رسمي](https://nodejs.org/docs/latest/api/path.html#path_path_normalize_path)

---

### path.parse(path)

- **path**: نص يمثل المسار
- **الوصف**: يحلل المسار إلى كائن (root, dir, base, ext, name)
- **مثال:**

```js
console.log(path.parse("/tmp/file.txt"));
```

[توثيق رسمي](https://nodejs.org/docs/latest/api/path.html#path_path_parse_path)

---

### path.format(pathObject)

- **pathObject**: كائن يحتوي على خصائص المسار (dir, base, ext, name)
- **الوصف**: يبني مسار من كائن.
- **مثال:**

```js
console.log(path.format({ dir: "/tmp", name: "file", ext: ".txt" }));
```

[توثيق رسمي](https://nodejs.org/docs/latest/api/path.html#path_path_format_pathobject)

---

### path.relative(from, to)

- **from**: المسار الأصلي
- **to**: المسار الهدف
- **الوصف**: يحسب المسار النسبي بين مسارين.
- **مثال:**

```js
console.log(path.relative("/data", "/data/app/file.js")); // app/file.js
```

[توثيق رسمي](https://nodejs.org/docs/latest/api/path.html#path_path_relative_from_to)

---

### path.sep

- **الوصف**: فاصل المسارات (\ أو / حسب النظام)
- **مثال:**

```js
console.log(path.sep); // على ويندوز: \\، على لينكس: /
```

[توثيق رسمي](https://nodejs.org/docs/latest/api/path.html#path_path_sep)

---

### path.delimiter

- **الوصف**: فاصل متغيرات البيئة (; أو : حسب النظام)
- **مثال:**

```js
console.log(path.delimiter); // ; على ويندوز، : على لينكس
```

[توثيق رسمي](https://nodejs.org/docs/latest/api/path.html#path_path_delimiter)

---

### path.win32 / path.posix

- **الوصف**: دوال path خاصة بكل نظام (للحصول على نتائج ثابتة)
- **مثال:**

```js
console.log(path.win32.basename("C:\\temp\\myfile.html"));
console.log(path.posix.basename("/tmp/myfile.html"));
```

[توثيق رسمي](https://nodejs.org/docs/latest/api/path.html#path_path_win32)

---

## حالات الاستخدام الشائعة

- توليد مسارات ملفات مؤقتة أو ديناميكية
- استخراج اسم الملف أو الامتداد من مسار
- بناء مسارات متوافقة مع النظام
- التحقق من المسارات المطلقة
- دعم أنظمة Windows وLinux في نفس الكود

---

## أفضل الممارسات

- استخدم join وresolve دائمًا لبناء المسارات
- استعمل parse وformat لتحليل أو بناء مسارات معقدة
- استخدم path.sep وpath.delimiter عند التعامل مع متغيرات البيئة
- تحقق من صحة المسارات القادمة من المستخدمين
- استخدم win32/posix إذا كنت تحتاج نتائج ثابتة

---

## التحذيرات الأمنية

- لا تثق في المسارات القادمة من المستخدمين، تحقق منها قبل استخدامها في عمليات حساسة
- تجنب دمج المسارات يدويًا لتفادي أخطاء الفواصل

---

## أدوات التصحيح المتعلقة

- [node --inspect](https://nodejs.org/en/docs/guides/debugging-getting-started/)
- [path-exists](https://www.npmjs.com/package/path-exists) (للتحقق من وجود المسار)

---

## اختبار تفاعلي

```js
const test = require("node:test");
const assert = require("node:assert");
const path = require("path");

test("اختبار path.join", () => {
  assert.strictEqual(path.join("dir", "sub", "file.txt"), "dir/sub/file.txt");
});
```

---

## نصائح الخبراء

- استخدم always join/resolve بدل التلاعب اليدوي بالنصوص
- استعمل parse/format مع المسارات المعقدة
- تحقق من النظام عند التعامل مع مسارات خارجية

---

## ملاحظات تقنية

- بعض الدوال تعطي نتائج مختلفة حسب النظام. استخدم win32/posix للثبات.
- resolve يحول أي مسار إلى مطلق بناءً على المسار الحالي.
- راجع [توثيق Node.js الرسمي - path](https://nodejs.org/docs/latest/api/path.html) لأي تحديثات.
