### process (إدارة العمليات في Node.js)
**الوصف**:
موديول process يوفر واجهة للتعامل مع عملية Node.js الحالية: معلومات النظام، البيئة، إدارة الإنهاء، التعامل مع الإشارات، الأحداث الحرجة، والتحكم في تدفق التنفيذ. يُستخدم في جميع تطبيقات Node.js تقريبًا.

---
#### أهم الخصائص والدوال:

```js
// argv: مصفوفة وسيطات التشغيل
console.log(process.argv); // ["node", "script.js", "arg1", ...]

// env: كائن متغيرات البيئة
console.log(process.env.PATH); // طباعة متغير PATH

// exit([code]): إنهاء العملية
process.exit(1); // إنهاء مع كود خطأ

// cwd(): الحصول على المسار الحالي
console.log(process.cwd());

// chdir(dir): تغيير المسار الحالي
process.chdir('/tmp');

// pid, ppid: معرف العملية والأب
console.log(process.pid, process.ppid);

// platform, arch: منصة النظام والمعمارية
console.log(process.platform, process.arch);

// memoryUsage(): إحصائيات الذاكرة
console.log(process.memoryUsage());

// uptime(): مدة التشغيل بالثواني
console.log(process.uptime());

// nextTick(callback): تنفيذ دالة في الدورة التالية من الحدث
process.nextTick(() => console.log('التالي'));

// stdin, stdout, stderr: تدفقات الإدخال/الإخراج
process.stdin.on('data', data => console.log('أدخلت:', data.toString()));
process.stdout.write('مرحبا!\n');
process.stderr.write('خطأ!\n');

// events: التعامل مع أحداث العملية
process.on('exit', code => console.log('انتهت العملية بكود:', code));
process.on('uncaughtException', err => console.error('خطأ غير معالج:', err));
```

---
#### شرح البارامترات والقيم:
- **argv**: مصفوفة (Array) تحتوي على اسم node، اسم السكريبت، ثم جميع الوسيطات.
- **env**: كائن (Object) يمثل متغيرات البيئة.
- **exit(code)**: ينهي العملية بكود (افتراضي 0 = نجاح).
- **cwd()**: يرجع المسار الحالي كسلسلة نصية.
- **chdir(dir)**: يغير المسار الحالي (يرمي خطأ إذا لم يوجد).
- **pid/ppid**: أرقام صحيحة (Integer) لمعرف العملية والأب.
- **platform/arch**: نصوص (String) مثل 'win32', 'linux', 'x64'.
- **memoryUsage()**: كائن بإحصائيات الذاكرة (heap, rss, external).
- **uptime()**: عدد ثواني التشغيل (Number).
- **nextTick(callback)**: دالة تُنفذ في أقرب دورة حدث.
- **stdin/stdout/stderr**: كائنات Stream (للقراءة/الكتابة).

---
#### أمثلة عملية:

**1. قراءة وسيطات التشغيل:**
```js
if (process.argv.includes('--help')) {
  console.log('طريقة الاستخدام: node app.js [خيارات]');
  process.exit(0);
}
```

**2. التعامل مع متغيرات البيئة:**
```js
const env = process.env.NODE_ENV || 'development';
console.log('بيئة التشغيل:', env);
```

**3. التعامل مع الأحداث الحرجة:**
```js
process.on('uncaughtException', (err) => {
  console.error('خطأ غير معالج:', err);
  // سجل الخطأ ثم إنهاء آمن
  process.exit(1);
});
```

**4. التعامل مع الإشارات (مثل SIGINT):**
```js
process.on('SIGINT', () => {
  console.log('تم الضغط على Ctrl+C. إنهاء آمن...');
  process.exit(0);
});
```

**5. مراقبة استهلاك الذاكرة:**
```js
setInterval(() => {
  const mem = process.memoryUsage();
  console.log('الذاكرة المستخدمة:', (mem.rss / 1024 / 1024).toFixed(2), 'MB');
}, 5000);
```

---
#### أخطاء شائعة وحلولها:
- **نسيان التعامل مع uncaughtException/unhandledRejection:** يؤدي لتعطل التطبيق فجأة. الحل: سجل الأخطاء وانهِ العملية بأمان.
- **تغيير المسار الحالي لمسار غير موجود:** يسبب خطأ. الحل: تحقق من وجود المسار قبل استخدام chdir.
- **استخدام process.exit() دون تنظيف الموارد:** قد يؤدي لفقدان بيانات أو ملفات مفتوحة. الحل: أغلق جميع الموارد قبل الخروج.
- **الاعتماد على متغيرات بيئة غير معرفة:** الحل: استخدم قيمة افتراضية أو تحقق من وجود المتغير.

---
#### نصائح احترافية:
- **لا تستخدم process.exit() إلا عند الضرورة القصوى**، لأنه ينهي العملية فورًا دون انتظار الأحداث أو إنهاء الاتصالات.
- **سجل جميع الأخطاء الحرجة** (uncaughtException, unhandledRejection) في ملف أو خدمة مراقبة.
- **استخدم process.env فقط لقراءة متغيرات البيئة**، ولا تعتمد على وجودها دائمًا في جميع البيئات.
- **راقب استهلاك الذاكرة والدورات الزمنية** في التطبيقات طويلة الأمد.
- **استخدم process.nextTick بحذر**: الإفراط في استخدامه قد يؤدي إلى جمود (starvation) في حلقة الأحداث.
- **عند التعامل مع stdin في تطبيقات CLI**، تأكد من وضع stdin في الوضع المناسب (raw, encoding).
- **لا تعتمد على platform/arch في منطق حساس** بدون اختبار عبر جميع الأنظمة المستهدفة.

---
#### ملاحظات تقنية:
- **الأحداث الحرجة:**
  - 'exit': يُطلق عند إنهاء العملية (لا يمكن تنفيذ عمليات async هنا).
  - 'uncaughtException': لالتقاط الأخطاء غير المعالجة (استخدمه فقط للتسجيل ثم إنهاء العملية).
  - 'SIGINT', 'SIGTERM': لالتقاط إشارات النظام (مفيدة للإغلاق الآمن).
- **الذاكرة:** استخدم process.memoryUsage لمراقبة التسريبات.
- **الأداء:** استخدم process.hrtime لقياس الزمن بدقة نانوية.
- **الأمان:** لا تعرض متغيرات البيئة الحساسة في السجلات أو رسائل الخطأ.

---
#### مصادر:
- [توثيق Node.js الرسمي - process](https://nodejs.org/docs/latest/api/process.html) 