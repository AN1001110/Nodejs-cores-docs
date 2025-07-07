# process (إدارة العمليات في Node.js)

## الوصف
موديول process يوفر واجهة للتعامل مع عملية Node.js الحالية: معلومات النظام، البيئة، إدارة الإنهاء، التعامل مع الإشارات، الأحداث الحرجة، والتحكم في تدفق التنفيذ. يُستخدم في جميع تطبيقات Node.js تقريبًا.

---

## فهرس الخصائص والدوال
| الخاصية/الدالة | الوصف |
|---------------|-------|
| [`process.argv`](#processargv) | وسيطات التشغيل |
| [`process.env`](#processenv) | متغيرات البيئة |
| [`process.exit`](#processexitcode) | إنهاء العملية |
| [`process.cwd`](#processcwd) | المسار الحالي |
| [`process.chdir`](#processchdirdir) | تغيير المسار الحالي |
| [`process.pid`/`process.ppid`](#processpid-ppid) | معرف العملية والأب |
| [`process.platform`/`process.arch`](#processplatform-arch) | منصة النظام والمعمارية |
| [`process.memoryUsage`](#processmemoryusage) | إحصائيات الذاكرة |
| [`process.uptime`](#processuptime) | مدة التشغيل |
| [`process.nextTick`](#processnexttickcallback) | تنفيذ دالة في الدورة التالية |
| [`process.stdin`/`stdout`/`stderr`](#processstdin-stdout-stderr) | تدفقات الإدخال/الإخراج |
| [`process.on`](#processonevent-listener) | التعامل مع أحداث العملية |

---

## شرح الخصائص والدوال الأساسية

### process.argv
- **الوصف**: مصفوفة تحتوي على اسم node، اسم السكريبت، ثم جميع الوسيطات.
- **مثال:**
```js
console.log(process.argv); // ["node", "script.js", "arg1", ...]
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/process.html#processargv)

---

### process.env
- **الوصف**: كائن يمثل متغيرات البيئة.
- **مثال:**
```js
console.log(process.env.PATH); // طباعة متغير PATH
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/process.html#processenv)

---

### process.exit([code])
- **code**: كود الخروج (افتراضي 0 = نجاح)
- **الوصف**: ينهي العملية بكود محدد.
- **مثال:**
```js
process.exit(1); // إنهاء مع كود خطأ
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/process.html#processexitcode)

---

### process.cwd()
- **الوصف**: يرجع المسار الحالي كسلسلة نصية.
- **مثال:**
```js
console.log(process.cwd());
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/process.html#processcwd)

---

### process.chdir(dir)
- **dir**: المسار الجديد (String)
- **الوصف**: يغير المسار الحالي (يرمي خطأ إذا لم يوجد).
- **مثال:**
```js
process.chdir('/tmp');
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/process.html#processchdirdirectory)

---

### process.pid / process.ppid
- **الوصف**: أرقام صحيحة لمعرف العملية والأب.
- **مثال:**
```js
console.log(process.pid, process.ppid);
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/process.html#processpid)

---

### process.platform / process.arch
- **الوصف**: نصوص مثل 'win32', 'linux', 'x64'.
- **مثال:**
```js
console.log(process.platform, process.arch);
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/process.html#processplatform)

---

### process.memoryUsage()
- **الوصف**: كائن بإحصائيات الذاكرة (heap, rss, external).
- **مثال:**
```js
console.log(process.memoryUsage());
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/process.html#processmemoryusage)

---

### process.uptime()
- **الوصف**: عدد ثواني التشغيل (Number).
- **مثال:**
```js
console.log(process.uptime());
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/process.html#processuptime)

---

### process.nextTick(callback)
- **callback**: دالة تُنفذ في أقرب دورة حدث.
- **الوصف**: تنفيذ دالة في الدورة التالية من الحدث.
- **مثال:**
```js
process.nextTick(() => console.log('التالي'));
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/process.html#processnexttickcallback-args)

---

### process.stdin / process.stdout / process.stderr
- **الوصف**: كائنات Stream (للقراءة/الكتابة).
- **مثال:**
```js
process.stdin.on('data', data => console.log('أدخلت:', data.toString()));
process.stdout.write('مرحبا!\n');
process.stderr.write('خطأ!\n');
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/process.html#processstdin)

---

### process.on(event, listener)
- **event**: اسم الحدث (مثل 'exit', 'uncaughtException', 'SIGINT')
- **listener**: دالة تُنفذ عند وقوع الحدث
- **الوصف**: التعامل مع أحداث العملية.
- **مثال:**
```js
process.on('exit', code => console.log('انتهت العملية بكود:', code));
process.on('uncaughtException', err => console.error('خطأ غير معالج:', err));
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/process.html#processoneventname-listener)

---

## حالات الاستخدام الشائعة
- قراءة وسيطات التشغيل
- التعامل مع متغيرات البيئة
- التعامل مع الأحداث الحرجة (exit, uncaughtException, SIGINT)
- مراقبة استهلاك الذاكرة
- بناء تطبيقات CLI

---

## أفضل الممارسات
- لا تستخدم process.exit() إلا عند الضرورة القصوى
- سجل جميع الأخطاء الحرجة (uncaughtException, unhandledRejection)
- استخدم process.env فقط لقراءة متغيرات البيئة
- راقب استهلاك الذاكرة والدورات الزمنية في التطبيقات طويلة الأمد
- استخدم process.nextTick بحذر
- تحقق من وجود المسار قبل استخدام chdir

---

## التحذيرات الأمنية
- لا تعرض متغيرات البيئة الحساسة في السجلات أو رسائل الخطأ
- لا تعتمد على platform/arch في منطق حساس بدون اختبار عبر جميع الأنظمة
- أغلق جميع الموارد قبل استخدام process.exit

---

## أدوات التصحيح المتعلقة
- [node --inspect](https://nodejs.org/en/docs/guides/debugging-getting-started/)
- [why-is-node-running](https://www.npmjs.com/package/why-is-node-running) (لمعرفة سبب استمرار العملية)

---

## اختبار تفاعلي
```js
const test = require('node:test');
const assert = require('node:assert');

test('اختبار argv', () => {
  assert.ok(Array.isArray(process.argv));
});
```

---

## نصائح الخبراء
- لا تستخدم process.exit() إلا عند الضرورة القصوى
- سجل جميع الأخطاء الحرجة في ملف أو خدمة مراقبة
- راقب استهلاك الذاكرة في التطبيقات طويلة الأمد
- تحقق من وجود متغيرات البيئة قبل الاعتماد عليها

---

## ملاحظات تقنية
- الأحداث الحرجة: 'exit', 'uncaughtException', 'SIGINT', 'SIGTERM'
- استخدم process.memoryUsage لمراقبة التسريبات
- استخدم process.hrtime لقياس الزمن بدقة نانوية
- راجع [توثيق Node.js الرسمي - process](https://nodejs.org/docs/latest/api/process.html) لأي تحديثات 