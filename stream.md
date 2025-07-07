### stream (تدفقات البيانات في Node.js)
**الوصف**:
موديول stream يوفر واجهة لمعالجة تدفقات البيانات (قراءة، كتابة، تحويل) بكفاءة عالية وذاكرة منخفضة. يُستخدم في التعامل مع الملفات الكبيرة، الشبكات، ضغط البيانات، وبناء بايبات (pipes) بين المصادر والمصارف.

---
#### أهم الكلاسات والدوال:

```js
const { Readable, Writable, Duplex, Transform, pipeline } = require('node:stream');

// Readable: تدفق للقراءة فقط
const readable = Readable.from(['a', 'b', 'c']);
readable.on('data', chunk => console.log('قراءة:', chunk.toString()));

// Writable: تدفق للكتابة فقط
const writable = new Writable({
  write(chunk, encoding, callback) {
    console.log('كتابة:', chunk.toString());
    callback();
  }
});
writable.write('مرحبا');

// Duplex: تدفق للقراءة والكتابة
const duplex = new Duplex({
  read(size) { this.push('بيانات'); this.push(null); },
  write(chunk, encoding, callback) { console.log('Duplex:', chunk.toString()); callback(); }
});
duplex.on('data', d => console.log('Duplex قراءة:', d.toString()));
duplex.write('اختبار');

// Transform: تحويل البيانات أثناء التدفق
const upper = new Transform({
  transform(chunk, encoding, callback) {
    callback(null, chunk.toString().toUpperCase());
  }
});
upper.on('data', d => console.log('تحويل:', d.toString()));
upper.write('hello');

// pipeline: ربط عدة تدفقات معًا (مع معالجة الأخطاء)
pipeline(
  Readable.from(['a', 'b', 'c']),
  upper,
  writable,
  (err) => { if (err) console.error('خطأ في البايب:', err); else console.log('تمت المعالجة!'); }
);
```

---
#### شرح البارامترات والقيم:
- **chunk**: جزء من البيانات (Buffer أو String).
- **encoding**: ترميز البيانات (عادة 'utf8').
- **callback**: دالة تُستدعى عند انتهاء المعالجة.
- **size**: حجم البيانات المطلوب قراءتها.
- **options**: كائن خيارات (object) لتخصيص التدفق.

---
#### أمثلة عملية:

**1. قراءة ملف كبير بسطر واحد:**
```js
const fs = require('fs');
const { pipeline } = require('node:stream');
pipeline(
  fs.createReadStream('bigfile.txt'),
  fs.createWriteStream('copy.txt'),
  (err) => { if (err) console.error('خطأ:', err); else console.log('تم النسخ!'); }
);
```

**2. ضغط ملف أثناء القراءة:**
```js
const zlib = require('zlib');
const fs = require('fs');
fs.createReadStream('input.txt')
  .pipe(zlib.createGzip())
  .pipe(fs.createWriteStream('output.txt.gz'));
```

**3. تحويل البيانات أثناء التدفق:**
```js
const { Transform } = require('node:stream');
const upper = new Transform({
  transform(chunk, enc, cb) { cb(null, chunk.toString().toUpperCase()); }
});
process.stdin.pipe(upper).pipe(process.stdout);
```

**4. استهلاك تدفق قابل للقراءة باستخدام async iterator:**
```js
const { Readable } = require('node:stream');
const readable = Readable.from(['سطر1', 'سطر2']);
(async () => {
  for await (const chunk of readable) {
    console.log('Async:', chunk.toString());
  }
})();
```

---
#### أخطاء شائعة وحلولها:
- **نسيان التعامل مع الأخطاء:** التدفقات قد ترمي أخطاء (error event). الحل: أضف always مستمع 'error' أو استخدم pipeline.
- **تحميل ملف كبير بالكامل في الذاكرة:** الحل: استخدم streams بدلاً من readFile/ writeFile للملفات الكبيرة.
- **نسيان إنهاء التدفق (end/close):** قد يؤدي لتوقف البرنامج أو فقدان البيانات. الحل: استمع لأحداث 'end' و 'finish'.
- **نسيان التعامل مع backpressure:** إذا كان المستهلك أبطأ من المنتج، قد تمتلئ الذاكرة. الحل: استخدم pipe أو pipeline لإدارة الضغط تلقائيًا.

---
#### نصائح احترافية:
- **استخدم pipeline دائمًا** لربط التدفقات، فهو يدير الأخطاء والضغط (backpressure) تلقائيًا.
- **لا تستخدم readFile/ writeFile مع الملفات الكبيرة**، بل استخدم createReadStream/ createWriteStream.
- **استمع دائمًا لحدث 'error'** في جميع التدفقات.
- **استخدم objectMode إذا كنت تتعامل مع كائنات وليس نصوص أو بايتات.**
- **استخدم async iterators** مع Readable streams للمعالجة الحديثة والسهلة.
- **اضبط highWaterMark** إذا احتجت لضبط حجم البفر حسب حجم البيانات.

---
#### ملاحظات تقنية:
- **streams تدعم أنماط القراءة/الكتابة/التحويل/الثنائية (objectMode).**
- **pipeline و finished** هما أفضل الطرق لإدارة التدفقات الحديثة.
- **backpressure**: آلية تمنع امتلاء الذاكرة عند تدفق البيانات بسرعة كبيرة.
- **التوافق:** معظم مكتبات Node.js (مثل http, fs, zlib) تعتمد على streams.

---
#### مصادر:
- [توثيق Node.js الرسمي - stream](https://nodejs.org/docs/latest/api/stream.html) 