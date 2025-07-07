### events (إدارة الأحداث في Node.js)
**الوصف**:
موديول events يوفر واجهة لإنشاء وإدارة الأحداث (EventEmitter)، وهو أساس نظام الأحداث في Node.js. يُستخدم في بناء تطبيقات تعتمد على الأحداث مثل الخوادم، التعامل مع البيانات المتدفقة، والتكامل مع مكتبات الطرف الثالث.

---
#### أهم الكلاسات والدوال:

```js
const EventEmitter = require('events');

// إنشاء كائن أحداث
const emitter = new EventEmitter();

// on(event, listener): إضافة مستمع
emitter.on('data', (msg) => console.log('وصلت رسالة:', msg));

// emit(event, ...args): إطلاق حدث
emitter.emit('data', 'مرحبا بالعالم');

// once(event, listener): مستمع لمرة واحدة فقط
emitter.once('init', () => console.log('تهيئة لمرة واحدة'));
emitter.emit('init'); // سيتم التنفيذ مرة واحدة فقط

// removeListener(event, listener) أو off(event, listener): إزالة مستمع
function handler() { console.log('تم الحذف'); }
emitter.on('remove', handler);
emitter.off('remove', handler);

// removeAllListeners([event]): إزالة جميع المستمعين
emitter.removeAllListeners('data');

// listeners(event): جلب جميع المستمعين لحدث معين
console.log(emitter.listeners('data'));

// eventNames(): جلب جميع أسماء الأحداث المسجلة
console.log(emitter.eventNames());

// setMaxListeners(n): تحديد الحد الأقصى للمستمعين
emitter.setMaxListeners(20);

// getMaxListeners(): جلب الحد الأقصى الحالي
console.log(emitter.getMaxListeners());
```

---
#### شرح البارامترات والقيم:
- **event**: اسم الحدث (String).
- **listener**: دالة تُنفذ عند إطلاق الحدث.
- **...args**: أي بيانات إضافية تُمرر للمستمع.
- **n**: عدد صحيح (Integer) للحد الأقصى للمستمعين.

---
#### أمثلة عملية:

**1. بناء نظام إشعارات بسيط:**
```js
const EventEmitter = require('events');
class Notifier extends EventEmitter {}
const notifier = new Notifier();
notifier.on('notify', (msg) => console.log('إشعار:', msg));
notifier.emit('notify', 'تمت العملية بنجاح!');
```

**2. التعامل مع الأخطاء:**
```js
emitter.on('error', (err) => {
  console.error('حدث خطأ:', err);
});
emitter.emit('error', new Error('فشل في الاتصال'));
```

**3. مستمع لمرة واحدة:**
```js
emitter.once('ready', () => console.log('جاهز للعمل!'));
emitter.emit('ready'); // سيتم التنفيذ مرة واحدة فقط
emitter.emit('ready'); // لن يتم التنفيذ
```

**4. منع تسرب الذاكرة (Memory Leak):**
```js
// إذا أضفت أكثر من 10 مستمعين لنفس الحدث، سيظهر تحذير
emitter.setMaxListeners(50); // زد الحد إذا كان ذلك مقصودًا
```

---
#### أخطاء شائعة وحلولها:
- **تسرب الذاكرة بسبب كثرة المستمعين:** إذا أضفت مستمعين كثيرين دون إزالة غير الضروريين، سيظهر تحذير (MaxListenersExceededWarning). الحل: استخدم removeListener أو removeAllListeners عند عدم الحاجة.
- **نسيان التعامل مع حدث 'error':** إذا تم إطلاق حدث 'error' بدون مستمع، ستنهار العملية. الحل: أضف دائمًا مستمع لحدث 'error'.
- **استخدام once بدل on أو العكس:** استخدم once للأحداث التي يجب أن تُعالج مرة واحدة فقط.
- **نسيان إزالة المستمعين في الكائنات المؤقتة:** يؤدي لتسرب الذاكرة. الحل: أزل المستمعين عند الانتهاء.

---
#### نصائح احترافية:
- **راقب عدد المستمعين:** إذا احتجت أكثر من 10 مستمعين، زد الحد بوضوح (setMaxListeners) أو راجع منطق التطبيق.
- **سجل جميع الأخطاء في مستمع 'error'** ولا تتركه فارغًا.
- **استخدم removeListener أو off** لإزالة المستمعين غير الضروريين لتقليل استهلاك الذاكرة.
- **استخدم once للأحداث التي تحدث مرة واحدة فقط** (تهيئة، إغلاق، إلخ).
- **تجنب تمرير كائنات ضخمة عبر الأحداث** لتقليل استهلاك الذاكرة.
- **استخدم eventNames و listeners** لمراقبة حالة الأحداث ديناميكيًا.

---
#### ملاحظات تقنية:
- **تسرب الذاكرة:** يحدث غالبًا عند إضافة مستمعين دون إزالة، خاصة في التطبيقات طويلة الأمد أو عند إنشاء كائنات EventEmitter مؤقتة.
- **الأداء:** إطلاق الأحداث (emit) سريع جدًا، لكن المستمعين الكُثر قد يؤثرون على الأداء.
- **الأمان:** لا تعتمد على الأحداث لنقل بيانات حساسة بين أجزاء غير موثوقة من التطبيق.
- **التوافق:** معظم مكتبات Node.js (مثل http, stream) ترث من EventEmitter.

---
#### مصادر:
- [توثيق Node.js الرسمي - events](https://nodejs.org/docs/latest/api/events.html) 