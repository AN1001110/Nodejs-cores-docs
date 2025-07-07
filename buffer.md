# buffer (التعامل مع البيانات الثنائية)

## الوصف
موديول buffer في Node.js يُستخدم للتعامل مع البيانات الثنائية (binary data) مثل الملفات، الشبكات، التشفير، وغيرها. Buffer هو مصفوفة بايتات ثابتة الطول، ويُستخدم بكثرة في عمليات القراءة/الكتابة منخفضة المستوى.

---

## فهرس الدوال والكلاسات
| الدالة/الكلاس | الوصف |
|---------------|-------|
| [`Buffer.alloc`](#bufferallocsize-fill-encoding) | إنشاء Buffer جديد مملوء بالأصفار |
| [`Buffer.allocUnsafe`](#bufferallocunsafesize) | إنشاء Buffer غير مهيأ (أسرع لكن غير آمن) |
| [`Buffer.from`](#bufferfromarray-string-buffer-encoding) | إنشاء Buffer من مصفوفة أو نص أو Buffer آخر |
| [`buf.readUInt8`](#bufreaduint8offset) | قراءة قيمة من البافر |
| [`buf.write`](#bufwritestring-offset-length-encoding) | كتابة نص في البافر |
| [`buf.toString`](#buftostringencoding-start-end) | تحويل البافر إلى نص |
| [`buf.slice`](#bufslicestart-end) | قص جزء من البافر (بدون نسخ) |
| [`buf.subarray`](#bufsubarraystart-end) | نسخ جزء من البافر (مع نسخ) |
| [`Buffer.compare`](#buffercomparebufa-bufb) | مقارنة بافرين |
| [`Buffer.concat`](#bufferconcatlist-totallength) | دمج عدة بافرات |
| [`buf.toJSON`](#buftojson) | تحويل البافر إلى JSON |
| [`buf.fill`](#buffillvalue-offset-end-encoding) | تعبئة البافر بقيمة |
| [`buf.readInt16LE`](#bufreadint16leoffset) / [`buf.writeInt16LE`](#bufwriteint16levalue-offset) | قراءة/كتابة أعداد صحيحة وعائمة |

---

## شرح الدوال والكلاسات الأساسية

### Buffer.alloc(size[, fill[, encoding]])
- **size**: حجم البافر بالبايت (Integer)
- **fill**: القيمة الافتراضية (String | Buffer | Integer, اختياري)
- **encoding**: الترميز إذا كان fill نص (String, افتراضي 'utf8')
- **الوصف**: إنشاء Buffer جديد مملوء بالأصفار أو بقيمة محددة.
- **مثال:**
```js
const buf1 = Buffer.alloc(10); // 10 بايت أصفار
const buf2 = Buffer.alloc(10, 1); // 10 بايت بقيمة 1
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/buffer.html#bufferallocsize-fill-encoding)

---

### Buffer.allocUnsafe(size)
- **size**: حجم البافر (Integer)
- **الوصف**: إنشاء Buffer غير مهيأ (أسرع لكن غير آمن).
- **مثال:**
```js
const buf3 = Buffer.allocUnsafe(10); // قد يحتوي بيانات قديمة
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/buffer.html#bufferallocunsafesize)

---

### Buffer.from(array | string | buffer[, encoding])
- **array**: مصفوفة أرقام
- **string**: نص
- **buffer**: بافر آخر
- **encoding**: الترميز (String, افتراضي 'utf8')
- **الوصف**: إنشاء Buffer من مصفوفة أو نص أو Buffer آخر.
- **مثال:**
```js
const buf4 = Buffer.from([1, 2, 3]);
const buf5 = Buffer.from('hello', 'utf8');
const buf6 = Buffer.from(buf4);
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/buffer.html#bufferfromarray)

---

### buf.readUInt8(offset)
- **offset**: موقع القراءة (Integer)
- **الوصف**: قراءة قيمة من البافر.
- **مثال:**
```js
const value = buf4.readUInt8(0); // 1
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/buffer.html#bufreaduint8offset)

---

### buf.write(string[, offset[, length]][, encoding])
- **string**: النص المراد كتابته (String)
- **offset**: موقع البداية (Integer, افتراضي 0)
- **length**: عدد البايتات (Integer, اختياري)
- **encoding**: الترميز (String, افتراضي 'utf8')
- **الوصف**: كتابة نص في البافر.
- **مثال:**
```js
const buf7 = Buffer.alloc(10);
buf7.write('abc', 0, 3, 'utf8');
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/buffer.html#bufwritestring-offset-length-encoding)

---

### buf.toString([encoding[, start[, end]]])
- **encoding**: الترميز (String, افتراضي 'utf8')
- **start**: البداية (Integer, افتراضي 0)
- **end**: النهاية (Integer, افتراضي buffer.length)
- **الوصف**: تحويل البافر إلى نص.
- **مثال:**
```js
const str = buf5.toString('utf8'); // 'hello'
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/buffer.html#buftostringencoding-start-end)

---

### buf.slice([start[, end]])
- **start**: البداية (Integer)
- **end**: النهاية (Integer)
- **الوصف**: قص جزء من البافر (بدون نسخ).
- **مثال:**
```js
const part = buf5.slice(1, 3); // Buffer يحتوي 'el'
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/buffer.html#bufslicestart-end)

---

### buf.subarray([start[, end]])
- **start**: البداية (Integer)
- **end**: النهاية (Integer)
- **الوصف**: نسخ جزء من البافر (مع نسخ).
- **مثال:**
```js
const sub = buf5.subarray(1, 3);
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/buffer.html#bufsubarraystart-end)

---

### Buffer.compare(bufA, bufB)
- **bufA, bufB**: البافرين للمقارنة (Buffer)
- **الوصف**: مقارنة بافرين (ترتيب بايتات).
- **مثال:**
```js
Buffer.compare(Buffer.from('a'), Buffer.from('b')); // -1
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/buffer.html#buffercomparebuf1-buf2)

---

### Buffer.concat(list[, totalLength])
- **list**: قائمة البافر (Array<Buffer>)
- **totalLength**: الطول الكلي (Integer, اختياري)
- **الوصف**: دمج عدة بافرات في بافر واحد.
- **مثال:**
```js
const merged = Buffer.concat([buf4, buf5]);
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/buffer.html#bufferconcatlist-totallength)

---

### buf.toJSON()
- **الوصف**: تحويل البافر إلى JSON.
- **مثال:**
```js
const json = buf4.toJSON();
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/buffer.html#buftojson)

---

### buf.fill(value[, offset[, end]][, encoding])
- **value**: القيمة (String | Buffer | Integer)
- **offset**: البداية (Integer)
- **end**: النهاية (Integer)
- **encoding**: الترميز (String)
- **الوصف**: تعبئة البافر بقيمة محددة.
- **مثال:**
```js
const buf8 = Buffer.alloc(5);
buf8.fill('a');
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/buffer.html#buffillvalue-offset-end-encoding)

---

### buf.readInt16LE(offset) / buf.writeInt16LE(value, offset)
- **offset**: الموقع (Integer)
- **value**: القيمة (Integer)
- **الوصف**: قراءة/كتابة أعداد صحيحة وعائمة (LE/BE)
- **مثال:**
```js
const buf9 = Buffer.alloc(2);
buf9.writeInt16LE(256, 0);
const num = buf9.readInt16LE(0); // 256
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/buffer.html#bufreadint16leoffset)

---

## حالات الاستخدام الشائعة
- التعامل مع الملفات الثنائية (صور، فيديو، إلخ)
- معالجة البيانات من الشبكة أو البروتوكولات منخفضة المستوى
- التشفير وفك التشفير
- بناء بروتوكولات خاصة

---

## أفضل الممارسات
- استخدم Buffer.alloc بدلاً من Buffer.allocUnsafe للحماية من البيانات القديمة
- تحقق دائماً من الطول قبل القراءة/الكتابة
- حدد الترميز المناسب عند التعامل مع نصوص غير UTF-8
- استخدم Buffer.concat عند التعامل مع بيانات متسلسلة

---

## التحذيرات الأمنية
- لا تستخدم Buffer.allocUnsafe بدون تعبئة مباشرة بعد الإنشاء
- لا تعرض محتوى البافر مباشرة للمستخدم النهائي إذا كان يحتوي بيانات حساسة

---

## أدوات التصحيح المتعلقة
- [node --inspect](https://nodejs.org/en/docs/guides/debugging-getting-started/)
- [buffer-crc32](https://www.npmjs.com/package/buffer-crc32) (حساب CRC)

---

## اختبار تفاعلي
```js
const test = require('node:test');
const assert = require('node:assert');

test('Buffer.alloc ينشئ بافر بالحجم الصحيح', () => {
  const buf = Buffer.alloc(8);
  assert.strictEqual(buf.length, 8);
});
```

---

## نصائح الخبراء
- استخدم Buffer.alloc للحماية من البيانات القديمة
- استخدم Buffer.concat مع البيانات المتسلسلة
- استخدم دوال القراءة/الكتابة المناسبة لنوع البيانات (LE/BE, Int/UInt)
- استفد من Buffer في التعامل مع الملفات الثنائية أو بناء بروتوكولات خاصة

---

## ملاحظات تقنية
- Buffer يدعم جميع دوال القراءة/الكتابة للأعداد (راجع التوثيق الرسمي)
- راجع [توثيق Node.js الرسمي - buffer](https://nodejs.org/docs/latest/api/buffer.html) لأي تحديثات 