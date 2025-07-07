### cluster (تشغيل عدة عمليات Node.js متوازية)
**الوصف**:
موديول cluster في Node.js يُستخدم لتشغيل عدة عمليات Node.js (workers) على نفس الخادم للاستفادة من جميع أنوية المعالج (CPU Cores). يسمح بتوزيع الحمل على أكثر من عملية، مما يحسن الأداء في تطبيقات الخوادم عالية الضغط.

#### جميع الدوال والخصائص غير المهملة:

```javascript
// التحقق من نوع العملية
cluster.isPrimary // true إذا كانت العملية الأساسية (master)
cluster.isWorker  // true إذا كانت العملية عامل (worker)

// إنشاء عمليات فرعية (workers)
cluster.fork([env])
// البارامترس:
// - env: (object) متغيرات البيئة للعملية الفرعية (اختياري)
// مثال:
const cluster = require('node:cluster');
if (cluster.isPrimary) {
  cluster.fork({ WORKER_TYPE: 'api' });
}

// الوصول إلى جميع العمال
cluster.workers // كائن يحتوي على جميع العمال الحاليين (worker.id => worker)

// الاستماع لأحداث العمال
cluster.on('exit', (worker, code, signal) => {
  console.log(`العامل ${worker.process.pid} خرج`);
});
cluster.on('online', (worker) => {
  console.log(`العامل ${worker.process.pid} جاهز`);
});

// إعداد خصائص الكلستر
cluster.setupPrimary([settings])
// البارامترس:
// - settings: (object) إعدادات مثل exec, args, silent, stdio
// مثال:
cluster.setupPrimary({ exec: 'worker.js', args: ['--mode', 'prod'] });

// خصائص مهمة:
cluster.settings // إعدادات الكلستر الحالية
cluster.schedulingPolicy // سياسة توزيع الاتصالات (افتراضي: round-robin)

// العامل الحالي (داخل worker فقط)
cluster.worker // كائن worker الحالي

// إرسال رسالة بين العمليات
worker.send(message[, sendHandle[, options]][, callback])
// البارامترس:
// - message: (object) الرسالة
// - sendHandle: (server/socket) مقبض لنقله (اختياري)
// - options: (object) خيارات إضافية
// - callback: (function) دالة بعد الإرسال
// مثال:
if (cluster.isWorker) {
  process.on('message', (msg) => {
    console.log('رسالة من الماستر:', msg);
  });
  process.send({ ready: true });
}

// إنهاء عامل
worker.kill([signal])
// البارامترس:
// - signal: (string) إشارة نظام (افتراضي SIGTERM)
// مثال:
worker.kill('SIGKILL');

// قطع الاتصال مع العامل
worker.disconnect()
// مثال:
worker.disconnect();

// أحداث مهمة:
// - 'fork': عند إنشاء عامل جديد
// - 'online': عند جاهزية العامل
// - 'exit': عند خروج العامل
// - 'listening': عند بدء العامل الاستماع على منفذ
// - 'message': عند استقبال رسالة

// مثال عملي كامل:
const cluster = require('node:cluster');
const http = require('node:http');
const numCPUs = require('node:os').availableParallelism();

if (cluster.isPrimary) {
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }
  cluster.on('exit', (worker, code, signal) => {
    console.log(`العامل ${worker.process.pid} خرج`);
  });
} else {
  http.createServer((req, res) => {
    res.writeHead(200);
    res.end('hello world\n');
  }).listen(8000);
  console.log(`العامل ${process.pid} بدأ`);
}
```

#### الأخطاء الشائعة:
- [❌] الاعتماد على بيانات في الذاكرة بين العمال → الحل: استخدم قاعدة بيانات أو Redis لمشاركة الحالة.
- [❌] عدم مراقبة خروج العمال وإعادة تشغيلهم → الحل: استمع لحدث 'exit' وأعد تشغيل العمال عند الحاجة.
- [❌] تجاهل اختلافات نظام التشغيل (مثل توزيع الاتصالات على ويندوز) → الحل: اختبر الكود على جميع الأنظمة المستهدفة.

#### نصائح الخبراء:
- [🚀] استخدم cluster للاستفادة القصوى من جميع أنوية المعالج في تطبيقات الخوادم.
- [💡] وزع الجلسات (sessions) عبر قاعدة بيانات أو Redis بدلاً من الاعتماد على الذاكرة المحلية.
- [⚠️] لا تعتمد على cluster في إدارة التوجيه (routing) أو تكرار البيانات، بل استخدمه فقط لتوزيع الحمل.
- [🚀] راقب العمال وأعد تشغيلهم تلقائياً عند الخروج غير المتوقع.
- [💡] استخدم cluster مع worker_threads إذا كنت بحاجة لعزل أقل وذاكرة مشتركة. 