### buffer (التعامل مع البيانات الثنائية)
**الوصف**:
موديول buffer في Node.js يُستخدم للتعامل مع البيانات الثنائية (binary data) مثل الملفات، الشبكات، التشفير، وغيرها. Buffer هو مصفوفة بايتات ثابتة الطول، ويُستخدم بكثرة في عمليات القراءة/الكتابة منخفضة المستوى.

#### جميع الدوال غير المهملة:

```javascript
// إنشاء Buffer جديد مملوء بالأصفار
Buffer.alloc(size[, fill[, encoding]])
// البارامترس:
// - size: (integer) حجم البافر بالبايت
// - fill: (string | Buffer | integer) القيمة الافتراضية (اختياري)
// - encoding: (string) الترميز إذا كان fill نص (افتراضي 'utf8')
// مثال:
const buf1 = Buffer.alloc(10); // 10 بايت أصفار
const buf2 = Buffer.alloc(10, 1); // 10 بايت بقيمة 1

// إنشاء Buffer غير مهيأ (أسرع لكن غير آمن)
Buffer.allocUnsafe(size)
// البارامترس:
// - size: (integer) حجم البافر
// مثال:
const buf3 = Buffer.allocUnsafe(10); // قد يحتوي بيانات قديمة

// إنشاء Buffer من مصفوفة أو نص أو Buffer آخر
Buffer.from(array | string | buffer[, encoding])
// البارامترس:
// - array: (Array) مصفوفة أرقام
// - string: (string) نص
// - buffer: (Buffer) بافر آخر
// - encoding: (string) الترميز (افتراضي 'utf8')
// مثال:
const buf4 = Buffer.from([1, 2, 3]);
const buf5 = Buffer.from('hello', 'utf8');
const buf6 = Buffer.from(buf4);

// قراءة قيمة من البافر
buf.readUInt8(offset)
// البارامترس:
// - offset: (integer) موقع القراءة
// مثال:
const value = buf4.readUInt8(0); // 1

// كتابة قيمة في البافر
buf.write(string[, offset[, length]][, encoding])
// البارامترس:
// - string: (string) النص المراد كتابته
// - offset: (integer) موقع البداية (افتراضي 0)
// - length: (integer) عدد البايتات (اختياري)
// - encoding: (string) الترميز (افتراضي 'utf8')
// مثال:
const buf7 = Buffer.alloc(10);
buf7.write('abc', 0, 3, 'utf8');

// تحويل البافر إلى نص
buf.toString([encoding[, start[, end]]])
// البارامترس:
// - encoding: (string) الترميز (افتراضي 'utf8')
// - start: (integer) البداية (افتراضي 0)
// - end: (integer) النهاية (افتراضي buffer.length)
// مثال:
const str = buf5.toString('utf8'); // 'hello'

// قص جزء من البافر (بدون نسخ)
buf.slice([start[, end]])
// البارامترس:
// - start: (integer) البداية
// - end: (integer) النهاية
// مثال:
const part = buf5.slice(1, 3); // Buffer يحتوي 'el'

// نسخ جزء من البافر (مع نسخ)
buf.subarray([start[, end]])
// البارامترس:
// - start: (integer) البداية
// - end: (integer) النهاية
// مثال:
const sub = buf5.subarray(1, 3);

// مقارنة بافرين
Buffer.compare(bufA, bufB)
// البارامترس:
// - bufA, bufB: (Buffer) البافرين للمقارنة
// مثال:
Buffer.compare(Buffer.from('a'), Buffer.from('b')); // -1

// دمج عدة بافرات
Buffer.concat(list[, totalLength])
// البارامترس:
// - list: (Array<Buffer>) قائمة البافر
// - totalLength: (integer) الطول الكلي (اختياري)
// مثال:
const merged = Buffer.concat([buf4, buf5]);

// تحويل البافر إلى JSON
buf.toJSON()
// مثال:
const json = buf4.toJSON();

// تعبئة البافر بقيمة محددة
buf.fill(value[, offset[, end]][, encoding])
// البارامترس:
// - value: (string | Buffer | integer)
// - offset: (integer) البداية
// - end: (integer) النهاية
// - encoding: (string) الترميز
// مثال:
const buf8 = Buffer.alloc(5);
buf8.fill('a');

// قراءة وكتابة أعداد صحيحة وعائمة (LE/BE)
buf.readInt16LE(offset)
buf.writeInt16LE(value, offset)
// ... (يوجد دوال كثيرة مشابهة)
// مثال:
const buf9 = Buffer.alloc(2);
buf9.writeInt16LE(256, 0);
const num = buf9.readInt16LE(0); // 256
```

#### الأخطاء الشائعة:
- [❌] استخدام Buffer.allocUnsafe بدون تعبئة → الحل: استخدم Buffer.alloc أو املأ البافر مباشرة بعد الإنشاء.
- [❌] تجاوز حدود البافر عند القراءة/الكتابة → الحل: تحقق دائماً من الطول قبل العمليات.
- [❌] نسيان الترميز عند التعامل مع نصوص غير UTF-8 → الحل: حدد الترميز المناسب دائماً.

#### نصائح الخبراء:
- [💡] استخدم Buffer.alloc بدلاً من Buffer.allocUnsafe للحماية من البيانات القديمة.
- [🚀] استخدم Buffer.concat عند التعامل مع بيانات متسلسلة (مثل الشبكات).
- [⚠️] لا تعرض محتوى البافر مباشرة للمستخدم النهائي إذا كان يحتوي بيانات حساسة.
- [💡] استخدم دوال القراءة/الكتابة المناسبة لنوع البيانات (LE/BE, Int/UInt).
- [🚀] استفد من Buffer في التعامل مع الملفات الثنائية (صور، فيديو، إلخ) أو عند بناء بروتوكولات خاصة. 