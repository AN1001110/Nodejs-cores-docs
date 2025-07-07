# events (إدارة الأحداث في Node.js)

## الوصف
موديول events يوفر واجهة لإنشاء وإدارة الأحداث (EventEmitter)، وهو أساس نظام الأحداث في Node.js. يُستخدم في بناء تطبيقات تعتمد على الأحداث مثل الخوادم، التعامل مع البيانات المتدفقة، والتكامل مع مكتبات الطرف الثالث.

---

## فهرس الكلاسات والدوال
| الكلاس/الدالة | الوصف |
|---------------|-------|
| [`EventEmitter`](#eventemitter) | الكلاس الأساسي لإدارة الأحداث |
| [`on`](#on) | إضافة مستمع لحدث |
| [`once`](#once) | مستمع لمرة واحدة |
| [`emit`](#emit) | إطلاق حدث |
| [`removeListener`/`off`](#removelisteneroff) | إزالة مستمع |
| [`removeAllListeners`](#removealllisteners) | إزالة جميع المستمعين |
| [`listeners`](#listeners) | جلب جميع المستمعين |
| [`eventNames`](#eventnames) | جلب جميع أسماء الأحداث |
| [`setMaxListeners`](#setmaxlisteners) | تحديد الحد الأقصى للمستمعين |
| [`getMaxListeners`](#getmaxlisteners) | جلب الحد الأقصى الحالي |

---

## شرح الكلاسات والدوال الأساسية

### EventEmitter
- **الوصف**: الكلاس الأساسي لإنشاء كائنات تدعم الأحداث.
- **مثال:**
```js
const EventEmitter = require('events');
const emitter = new EventEmitter();
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/events.html#class-eventemitter)

---

### on(event, listener)
- **event**: اسم الحدث (String)
- **listener**: دالة تُنفذ عند إطلاق الحدث
- **الوصف**: إضافة مستمع دائم لحدث معين.
- **مثال:**
```js
emitter.on('data', (msg) => console.log('وصلت رسالة:', msg));
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/events.html#emitteroneventname-listener)

---

### once(event, listener)
- **event**: اسم الحدث
- **listener**: دالة تُنفذ مرة واحدة فقط
- **الوصف**: إضافة مستمع يُنفذ لمرة واحدة فقط عند إطلاق الحدث.
- **مثال:**
```js
emitter.once('init', () => console.log('تهيئة لمرة واحدة'));
emitter.emit('init');
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/events.html#emitteronceeventname-listener)

---

### emit(event, ...args)
- **event**: اسم الحدث
- **...args**: بيانات إضافية تُمرر للمستمعين
- **الوصف**: إطلاق حدث وتنفيذ جميع المستمعين المسجلين له.
- **مثال:**
```js
emitter.emit('data', 'مرحبا بالعالم');
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/events.html#emitteremiteventname-args)

---

### removeListener(event, listener) / off(event, listener)
- **event**: اسم الحدث
- **listener**: الدالة المراد إزالتها
- **الوصف**: إزالة مستمع معين من حدث.
- **مثال:**
```js
function handler() { console.log('تم الحذف'); }
emitter.on('remove', handler);
emitter.off('remove', handler);
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/events.html#emitteroffeventname-listener)

---

### removeAllListeners([event])
- **event**: اسم الحدث (اختياري)
- **الوصف**: إزالة جميع المستمعين لحدث معين أو لكل الأحداث.
- **مثال:**
```js
emitter.removeAllListeners('data');
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/events.html#emitterremovealllistenerseventname)

---

### listeners(event)
- **event**: اسم الحدث
- **الوصف**: جلب جميع المستمعين لحدث معين.
- **مثال:**
```js
console.log(emitter.listeners('data'));
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/events.html#emitterlistenerseventname)

---

### eventNames()
- **الوصف**: جلب جميع أسماء الأحداث المسجلة.
- **مثال:**
```js
console.log(emitter.eventNames());
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/events.html#emittereventnames)

---

### setMaxListeners(n)
- **n**: عدد صحيح (Integer) للحد الأقصى للمستمعين
- **الوصف**: تحديد الحد الأقصى لعدد المستمعين لكل حدث.
- **مثال:**
```js
emitter.setMaxListeners(20);
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/events.html#emittersetmaxlistenersn)

---

### getMaxListeners()
- **الوصف**: جلب الحد الأقصى الحالي للمستمعين.
- **مثال:**
```js
console.log(emitter.getMaxListeners());
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/events.html#emittergetmaxlisteners)

---

## حالات الاستخدام الشائعة
- بناء أنظمة إشعارات أو مراقبة
- التعامل مع تدفقات البيانات (stream)
- إدارة الأحداث في الخوادم أو تطبيقات الويب
- التكامل مع مكتبات تعتمد الأحداث

---

## أفضل الممارسات
- راقب عدد المستمعين واستخدم setMaxListeners عند الحاجة
- أضف دائمًا مستمع لحدث 'error' لتجنب انهيار التطبيق
- أزل المستمعين غير الضروريين لتقليل استهلاك الذاكرة
- استخدم once للأحداث التي تحدث مرة واحدة فقط

---

## التحذيرات الأمنية
- لا تعتمد على الأحداث لنقل بيانات حساسة بين أجزاء غير موثوقة
- تعامل مع الأخطاء دائمًا في مستمع 'error'

---

## أدوات التصحيح المتعلقة
- [node --inspect](https://nodejs.org/en/docs/guides/debugging-getting-started/)
- [eventemitter3](https://www.npmjs.com/package/eventemitter3) (بديل خفيف وسريع)

---

## اختبار تفاعلي
```js
const test = require('node:test');
const assert = require('node:assert');
const EventEmitter = require('events');

test('اختبار on/emit', () => {
  const emitter = new EventEmitter();
  let result = '';
  emitter.on('msg', (txt) => { result = txt; });
  emitter.emit('msg', 'ok');
  assert.strictEqual(result, 'ok');
});
```

---

## نصائح الخبراء
- راقب عدد المستمعين لتجنب تسرب الذاكرة
- استخدم once للأحداث التي تحدث مرة واحدة فقط
- استخدم eventNames و listeners لمراقبة حالة الأحداث

---

## ملاحظات تقنية
- معظم مكتبات Node.js (مثل http, stream) ترث من EventEmitter
- إطلاق الأحداث (emit) سريع جدًا لكن المستمعين الكُثر قد يؤثرون على الأداء
- راجع [توثيق Node.js الرسمي - events](https://nodejs.org/docs/latest/api/events.html) لأي تحديثات 