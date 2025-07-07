# stream (تدفقات البيانات في Node.js)

## الوصف
موديول stream يوفر واجهة قوية لمعالجة تدفقات البيانات (قراءة، كتابة، تحويل) بكفاءة عالية وذاكرة منخفضة. يُستخدم في التعامل مع الملفات الكبيرة، الشبكات، ضغط البيانات، وبناء بايبات (pipes) بين المصادر والمصارف.

---

## فهرس الكلاسات والدوال
| الكلاس/الدالة | الوصف |
|---------------|-------|
| [`Readable`](#readable) | تدفق للقراءة فقط |
| [`Writable`](#writable) | تدفق للكتابة فقط |
| [`Duplex`](#duplex) | تدفق للقراءة والكتابة |
| [`Transform`](#transform) | تحويل البيانات أثناء التدفق |
| [`pipeline`](#pipeline) | ربط عدة تدفقات معًا |
| [`finished`](#finished) | معرفة انتهاء التدفق |

---

## شرح الكلاسات والدوال الأساسية

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
[توثيق رسمي](https://nodejs.org/docs/latest/api/stream.html#class-streamreadable)

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
[توثيق رسمي](https://nodejs.org/docs/latest/api/stream.html#class-streamwritable)

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
[توثيق رسمي](https://nodejs.org/docs/latest/api/stream.html#class-streamduplex)

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
[توثيق رسمي](https://nodejs.org/docs/latest/api/stream.html#class-streamtransform)

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
[توثيق رسمي](https://nodejs.org/docs/latest/api/stream.html#streampipeline)

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
[توثيق رسمي](https://nodejs.org/docs/latest/api/stream.html#streamfinishedstream-callback)

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

---

## التحذيرات الأمنية
- لا تثق في البيانات القادمة من تدفقات خارجية (تحقق منها قبل المعالجة)
- تعامل مع الأخطاء دائمًا لتجنب توقف البرنامج

---

## أدوات التصحيح المتعلقة
- [node --inspect](https://nodejs.org/en/docs/guides/debugging-getting-started/)
- [stream-meter](https://www.npmjs.com/package/stream-meter) (لقياس حجم البيانات)

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
```

---

## نصائح الخبراء
- استخدم pipeline/finished بدل التعامل اليدوي مع الأحداث
- استخدم objectMode للبيانات غير النصية
- اضبط highWaterMark حسب احتياجك

---

## ملاحظات تقنية
- معظم مكتبات Node.js (مثل http, fs, zlib) تعتمد على streams
- backpressure يمنع امتلاء الذاكرة عند تدفق البيانات بسرعة
- راجع [توثيق Node.js الرسمي - stream](https://nodejs.org/docs/latest/api/stream.html) لأي تحديثات 