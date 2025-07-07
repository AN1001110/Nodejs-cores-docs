# stream (تدفقات البيانات في Node.js)

---

## الوصف
موديول `stream` يوفر واجهة قوية لمعالجة تدفقات البيانات (قراءة، كتابة، تحويل) بكفاءة عالية وذاكرة منخفضة. يُستخدم في التعامل مع الملفات الكبيرة، الشبكات، ضغط البيانات، وبناء بايبات (pipes) بين المصادر والمصارف.

---

## فهرس الكلاسات والدوال (جدول سريع)
| الكلاس/الدالة | الوصف | متوافق منذ |
|---------------|-------|------------|
| [`Readable`](#readable) | تدفق للقراءة فقط | دائمًا |
| [`Writable`](#writable) | تدفق للكتابة فقط | دائمًا |
| [`Duplex`](#duplex) | تدفق للقراءة والكتابة | دائمًا |
| [`Transform`](#transform) | تحويل البيانات أثناء التدفق | دائمًا |
| [`pipeline`](#pipeline) | ربط عدة تدفقات معًا | v10.0.0 |
| [`finished`](#finished) | معرفة انتهاء التدفق | v10.0.0 |
| [`PassThrough`](#passthrough) | تدفق يمرر البيانات كما هي | دائمًا |
| [`Stream.pipeline`](#streampipeline) | ربط تدفقات مع إدارة الأخطاء | v10.0.0 |
| [`Stream.finished`](#streamfinished) | معرفة انتهاء تدفق | v10.0.0 |
| [`stream.promises`](#streampromises) | واجهة وعود للتعامل مع التدفقات | v15.0.0 |

---

## مخطط مرئي (Mermaid) لتدفق البيانات
```mermaid
graph TD;
  A[مصدر البيانات] --> B[Readable]
  B --> C[Transform (اختياري)]
  C --> D[Writable]
  B --> D
```

---

## شرح الكلاسات والدوال الأساسية والموسعة

### Readable
- **الوصف**: كلاس لإنشاء تدفق قابل للقراءة فقط.
- **أهم البارامترات:**
  - **options**: كائن خيارات (object) مثل highWaterMark, encoding, objectMode
- **أهم الأحداث:**
  - **'data'**: عند وصول جزء جديد
  - **'end'**: عند انتهاء البيانات
  - **'error'**: عند حدوث خطأ
- **مثال:**
```js
const { Readable } = require('node:stream');
const readable = Readable.from(['a', 'b', 'c']);
readable.on('data', chunk => console.log('قراءة:', chunk.toString()));
```

---

### Writable
- **الوصف**: كلاس لإنشاء تدفق قابل للكتابة فقط.
- **أهم البارامترات:**
  - **write(chunk, encoding, callback)**: دالة الكتابة
  - **options**: كائن خيارات مثل highWaterMark, decodeStrings
- **أهم الأحداث:**
  - **'finish'**: عند انتهاء الكتابة
  - **'error'**: عند حدوث خطأ
- **مثال:**
```js
const { Writable } = require('node:stream');
const writable = new Writable({
  write(chunk, encoding, callback) {
    console.log('كتابة:', chunk.toString());
    callback();
  }
});
writable.write('مرحبا');
```

---

### Duplex
- **الوصف**: كلاس يجمع بين القراءة والكتابة (مثل socket).
- **أهم البارامترات:**
  - **read(size)**: دالة القراءة
  - **write(chunk, encoding, callback)**: دالة الكتابة
  - **options**: كائن خيارات
- **مثال:**
```js
const { Duplex } = require('node:stream');
const duplex = new Duplex({
  read(size) { this.push('بيانات'); this.push(null); },
  write(chunk, encoding, callback) { console.log('Duplex:', chunk.toString()); callback(); }
});
duplex.on('data', d => console.log('Duplex قراءة:', d.toString()));
duplex.write('اختبار');
```

---

### Transform
- **الوصف**: كلاس لتحويل البيانات أثناء التدفق (مثل ضغط أو تشفير).
- **أهم البارامترات:**
  - **transform(chunk, encoding, callback)**: دالة التحويل
  - **options**: كائن خيارات
- **مثال:**
```js
const { Transform } = require('node:stream');
const upper = new Transform({
  transform(chunk, encoding, callback) {
    callback(null, chunk.toString().toUpperCase());
  }
});
upper.on('data', d => console.log('تحويل:', d.toString()));
upper.write('hello');
```

---

### PassThrough
- **الوصف**: تدفق يمرر البيانات كما هي (مفيد للاختبار أو القياس).
- **مثال:**
```js
const { PassThrough } = require('node:stream');
const pass = new PassThrough();
pass.on('data', d => console.log('مرر:', d.toString()));
pass.write('test');
```

---

### pipeline(...streams, callback)
- **الوصف**: ربط عدة تدفقات معًا مع إدارة الأخطاء والضغط (backpressure).
- **البارامترات:**
  - **...streams**: قائمة التدفقات
  - **callback**: دالة تُستدعى عند الانتهاء أو الخطأ
- **مثال:**
```js
const { pipeline, Readable, Writable } = require('node:stream');
pipeline(
  Readable.from(['a', 'b', 'c']),
  new Writable({ write(chunk, enc, cb) { console.log(chunk.toString()); cb(); } }),
  (err) => { if (err) console.error('خطأ في البايب:', err); else console.log('تمت المعالجة!'); }
);
```

---

### finished(stream, callback)
- **الوصف**: معرفة انتهاء تدفق (قراءة أو كتابة) أو حدوث خطأ.
- **البارامترات:**
  - **stream**: التدفق المراد مراقبته
  - **callback**: دالة تُستدعى عند الانتهاء أو الخطأ
- **مثال:**
```js
const { finished, Readable } = require('node:stream');
const readable = Readable.from(['a', 'b']);
finished(readable, (err) => {
  if (err) console.error('خطأ:', err);
  else console.log('انتهى التدفق!');
});
```

---

### stream.promises
- **الوصف**: واجهة وعود (Promises) للتعامل مع التدفقات بشكل async/await.
- **مثال:**
```js
const { pipeline, Readable, Writable, promises: streamPromises } = require('node:stream');
(async () => {
  await streamPromises.pipeline(
    Readable.from(['a', 'b']),
    new Writable({ write(chunk, enc, cb) { cb(); } })
  );
})();
```

---

## مقارنة بين أنواع التدفقات
| الكلاس | قراءة | كتابة | تحويل |
|--------|-------|-------|--------|
| Readable | ✔️ | ❌ | ❌ |
| Writable | ❌ | ✔️ | ❌ |
| Duplex | ✔️ | ✔️ | ❌ |
| Transform | ✔️ | ✔️ | ✔️ |
| PassThrough | ✔️ | ✔️ | ❌ (بدون تحويل) |

---

## حالات الاستخدام الشائعة
- قراءة/كتابة ملفات كبيرة بكفاءة
- ضغط أو فك ضغط البيانات أثناء النقل
- بناء بايبات معالجة بيانات (مثل تحويل نصوص أو تشفير)
- التعامل مع تدفقات الشبكة (sockets)
- استهلاك تدفقات قابلة للقراءة باستخدام async iterator

---

## أفضل الممارسات
- استخدم pipeline دائمًا لربط التدفقات
- استمع دائمًا لحدث 'error' في جميع التدفقات
- استخدم objectMode إذا كنت تتعامل مع كائنات
- اضبط highWaterMark حسب حجم البيانات
- استخدم async iterators مع Readable streams
- تعامل مع الأخطاء في جميع callbacks

---

## التحذيرات الأمنية
- لا تثق في البيانات القادمة من تدفقات خارجية (تحقق منها قبل المعالجة)
- تعامل مع الأخطاء دائمًا لتجنب توقف البرنامج
- لا تترك تدفقات مفتوحة بدون إنهاء

---

## أدوات التصحيح المتعلقة
- [node --inspect](https://nodejs.org/en/docs/guides/debugging-getting-started/)
- [stream-meter](https://www.npmjs.com/package/stream-meter) (لقياس حجم البيانات)
- [why-is-node-running](https://www.npmjs.com/package/why-is-node-running)

---

## توافق الإصدارات
- معظم الكلاسات الأساسية متوفرة منذ الإصدارات الأولى
- خصائص مثل pipeline, finished, stream.promises متوفرة في الإصدارات الحديثة فقط
- راجع [توثيق Node.js الرسمي - stream](https://nodejs.org/docs/latest/api/stream.html) لأي تحديثات

---

## اختبار تفاعلي
```js
const test = require('node:test');
const assert = require('node:assert');
const { Readable } = require('node:stream');

test('اختبار Readable.from', async () => {
  const readable = Readable.from(['a', 'b']);
  let result = '';
  for await (const chunk of readable) result += chunk;
  assert.strictEqual(result, 'ab');
});

test('اختبار pipeline', async () => {
  const { pipeline, Readable, Writable } = require('node:stream/promises');
  let out = '';
  await pipeline(
    Readable.from(['x', 'y']),
    new Writable({ write(chunk, enc, cb) { out += chunk; cb(); } })
  );
  assert.strictEqual(out, 'xy');
});
```

---

## نصائح الخبراء
- استخدم pipeline/finished بدل التعامل اليدوي مع الأحداث
- استخدم objectMode للبيانات غير النصية
- اضبط highWaterMark حسب احتياجك
- تعامل مع الأخطاء في جميع التدفقات

---

## ملاحظات تقنية
- معظم مكتبات Node.js (مثل http, fs, zlib) تعتمد على streams
- backpressure يمنع امتلاء الذاكرة عند تدفق البيانات بسرعة
- راجع [توثيق Node.js الرسمي - stream](https://nodejs.org/docs/latest/api/stream.html) لأي تحديثات 

---

## أمثلة شاملة متقدمة

### مثال 1: قراءة ملف كبير وتحويله إلى أحرف كبيرة أثناء الكتابة
```js
const fs = require('fs');
const { Transform, pipeline } = require('node:stream');
const upper = new Transform({
  transform(chunk, encoding, callback) {
    callback(null, chunk.toString().toUpperCase());
  }
});
pipeline(
  fs.createReadStream('input.txt'),
  upper,
  fs.createWriteStream('output.txt'),
  err => {
    if (err) return console.error('خطأ في المعالجة:', err);
    console.log('تم التحويل والكتابة!');
  }
);
```
**شرح:** يوضح كيفية بناء خط معالجة متكامل مع معالجة الأخطاء.

---

### مثال 2: استهلاك تدفق قابل للقراءة باستخدام async iterator
```js
const { Readable } = require('node:stream');
const readable = Readable.from(['a', 'b', 'c']);
(async () => {
  let result = '';
  for await (const chunk of readable) {
    result += chunk;
  }
  console.log('النتيجة:', result);
})();
```
**شرح:** يوضح استخدام async iterator مع التدفقات.

---

### مثال 3: بناء Duplex مخصص (قراءة وكتابة)
```js
const { Duplex } = require('node:stream');
const duplex = new Duplex({
  read(size) {
    this.push('بيانات');
    this.push(null);
  },
  write(chunk, encoding, callback) {
    console.log('Duplex كتب:', chunk.toString());
    callback();
  }
});
duplex.on('data', d => console.log('Duplex قرأ:', d.toString()));
duplex.write('اختبار');
```
**شرح:** يوضح كيفية بناء Duplex مخصص.

---

### مثال 4: التعامل مع الأخطاء في التدفقات
```js
const { Readable } = require('node:stream');
const readable = new Readable({
  read() { this.emit('error', new Error('خطأ مخصص!')); }
});
readable.on('error', err => console.error('تم التقاط الخطأ:', err.message));
readable.read();
```
**شرح:** يوضح أهمية التعامل مع الأخطاء في التدفقات.

---

### مثال 5: استخدام stream.promises مع await
```js
const { pipeline, Readable, Writable, promises: streamPromises } = require('node:stream');
(async () => {
  let out = '';
  await streamPromises.pipeline(
    Readable.from(['x', 'y']),
    new Writable({ write(chunk, enc, cb) { out += chunk; cb(); } })
  );
  console.log('النتيجة:', out);
})();
```
**شرح:** يوضح استخدام وعود التدفق مع await.

--- 