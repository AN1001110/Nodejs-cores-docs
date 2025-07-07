### child_process (تشغيل العمليات الفرعية)
**الوصف**:
موديول child_process في Node.js يُستخدم لتشغيل أوامر النظام أو برامج خارجية من داخل تطبيق Node.js. يُستخدم في تنفيذ سكريبتات، إدارة عمليات، أو حتى بناء خطوط معالجة متقدمة (pipelines) بين العمليات.

#### جميع الدوال غير المهملة:

```javascript
// تنفيذ أمر نظامي بسيط (خيار مناسب للأوامر القصيرة)
child_process.exec(command[, options][, callback])
// البارامترس:
// - command: (string) الأمر المراد تنفيذه (مثال: 'ls -la')
// - options: (object) خيارات إضافية { cwd, env, encoding, timeout, maxBuffer, shell }
// - callback: (function) دالة تُستدعى بعد التنفيذ: function(error, stdout, stderr)
// مثال:
const { exec } = require('child_process');
exec('node -v', (error, stdout, stderr) => {
  if (error) return console.error('خطأ في التنفيذ:', error);
  console.log('إصدار Node:', stdout.trim());
});

// تنفيذ أمر مع التحكم الكامل في الإدخال/الإخراج
child_process.spawn(command[, args][, options])
// البارامترس:
// - command: (string) اسم البرنامج أو الأمر
// - args: (Array) مصفوفة بارامترس للأمر
// - options: (object) خيارات إضافية { cwd, env, stdio, detached, shell }
// مثال:
const { spawn } = require('child_process');
const ls = spawn('ls', ['-lh', '/usr']);
ls.stdout.on('data', (data) => {
  console.log('المخرجات:', data.toString());
});
ls.stderr.on('data', (data) => {
  console.error('خطأ:', data.toString());
});
ls.on('close', (code) => {
  console.log('انتهت العملية بكود:', code);
});

// تنفيذ أمر وانتظار النتيجة (synchronous, يحجب الـ event loop)
child_process.execSync(command[, options])
// البارامترس:
// - command: (string) الأمر
// - options: (object) خيارات إضافية { cwd, env, encoding, timeout, maxBuffer, stdio, shell }
// مثال:
const { execSync } = require('child_process');
const version = execSync('node -v', { encoding: 'utf8' });
console.log('إصدار Node:', version.trim());

// تنفيذ أمر بشكل متزامن مع التحكم في الإدخال/الإخراج
child_process.spawnSync(command[, args][, options])
// البارامترس:
// - command: (string) اسم البرنامج
// - args: (Array) مصفوفة بارامترس
// - options: (object) خيارات إضافية { cwd, env, input, stdio, shell, encoding }
// مثال:
const { spawnSync } = require('child_process');
const result = spawnSync('ls', ['-lh', '/usr'], { encoding: 'utf8' });
console.log('المخرجات:', result.stdout);

// تنفيذ أمر مع تمرير نص برمجي (يُستخدم مع node فقط غالباً)
child_process.fork(modulePath[, args][, options])
// البارامترس:
// - modulePath: (string) مسار ملف جافاسكريبت
// - args: (Array) مصفوفة بارامترس
// - options: (object) خيارات إضافية { cwd, env, execPath, execArgv, stdio, detached }
// مثال:
const { fork } = require('child_process');
const child = fork('worker.js', ['--mode', 'test']);
child.on('message', (msg) => {
  console.log('رسالة من العملية الفرعية:', msg);
});
child.send({ hello: 'world' });
```

#### الأخطاء الشائعة:
- [❌] عدم التعامل مع الأخطاء في callback أو الأحداث → الحل: استمع دائماً للأحداث ('error', 'close') وعالج الأخطاء.
- [❌] استخدام exec مع مدخلات المستخدم مباشرة (حقن أوامر) → الحل: استخدم spawn مع مصفوفة بارامترس ولا تمرر مدخلات المستخدم مباشرة للأمر.
- [❌] تجاهل المخرجات الكبيرة (stdout/stderr) في exec/spawn → الحل: استخدم maxBuffer أو استمع للأحداث لتفادي امتلاء الذاكرة.
- [❌] استخدام execSync/spawnSync في الخادم → الحل: تجنب الدوال المتزامنة في بيئة الخوادم.

#### نصائح الخبراء:
- [⚠️] لا تمرر مدخلات المستخدم مباشرة إلى exec أو shell: استخدم دائماً spawn مع مصفوفة بارامترس لتجنب حقن الأوامر (Command Injection).
- [💡] استخدم الأحداث ('exit', 'close', 'error') لمراقبة حالة العملية الفرعية.
- [🚀] استخدم fork لتشغيل عمليات Node.js فرعية والتواصل بينها عبر الرسائل (IPC).
- [💡] حدد maxBuffer في exec/execSync إذا كنت تتوقع مخرجات كبيرة.
- [🚀] استخدم stdio: 'inherit' في spawn/spawnSync إذا أردت تمرير الإدخال/الإخراج مباشرة من/إلى الطرفية.
- [⚠️] لا تستخدم الدوال المتزامنة (Sync) في تطبيقات الخادم لأنها تحجب الـ event loop وتؤثر على الأداء. 