# child_process (تشغيل العمليات الفرعية)

## الوصف
موديول child_process في Node.js يُستخدم لتشغيل أوامر النظام أو برامج خارجية من داخل تطبيق Node.js. يُستخدم في تنفيذ سكريبتات، إدارة عمليات، أو حتى بناء خطوط معالجة متقدمة (pipelines) بين العمليات.

---

## فهرس الدوال
| الدالة | الوصف |
|--------|-------|
| [`child_process.exec`](#child_processexeccommand-options-callback) | تنفيذ أمر نظامي بسيط |
| [`child_process.spawn`](#child_processspawncommand-args-options) | تنفيذ أمر مع تحكم كامل في الإدخال/الإخراج |
| [`child_process.execSync`](#child_processexecsynccommand-options) | تنفيذ أمر بشكل متزامن |
| [`child_process.spawnSync`](#child_processspawnsynccommand-args-options) | تنفيذ أمر متزامن مع تحكم في الإدخال/الإخراج |
| [`child_process.fork`](#child_processforkmodulepath-args-options) | تنفيذ نص برمجي كعملية فرعية |

---

## شرح الدوال الأساسية

### child_process.exec(command[, options][, callback])
- **command**: الأمر المراد تنفيذه (String)
- **options**: خيارات إضافية { cwd, env, encoding, timeout, maxBuffer, shell } (Object, اختياري)
- **callback**: دالة تُستدعى بعد التنفيذ: function(error, stdout, stderr)
- **الوصف**: تنفيذ أمر نظامي بسيط (مناسب للأوامر القصيرة).
- **مثال:**
```js
const { exec } = require('child_process');
exec('node -v', (error, stdout, stderr) => {
  if (error) return console.error('خطأ في التنفيذ:', error);
  console.log('إصدار Node:', stdout.trim());
});
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/child_process.html#child_processexeccommand-options-callback)

---

### child_process.spawn(command[, args][, options])
- **command**: اسم البرنامج أو الأمر (String)
- **args**: مصفوفة بارامترس للأمر (Array, اختياري)
- **options**: خيارات إضافية { cwd, env, stdio, detached, shell } (Object, اختياري)
- **الوصف**: تنفيذ أمر مع التحكم الكامل في الإدخال/الإخراج.
- **مثال:**
```js
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
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/child_process.html#child_processspawncommand-args-options)

---

### child_process.execSync(command[, options])
- **command**: الأمر (String)
- **options**: خيارات إضافية { cwd, env, encoding, timeout, maxBuffer, stdio, shell } (Object, اختياري)
- **الوصف**: تنفيذ أمر وانتظار النتيجة (synchronous, يحجب الـ event loop).
- **مثال:**
```js
const { execSync } = require('child_process');
const version = execSync('node -v', { encoding: 'utf8' });
console.log('إصدار Node:', version.trim());
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/child_process.html#child_processexecsynccommand-options)

---

### child_process.spawnSync(command[, args][, options])
- **command**: اسم البرنامج (String)
- **args**: مصفوفة بارامترس (Array, اختياري)
- **options**: خيارات إضافية { cwd, env, input, stdio, shell, encoding } (Object, اختياري)
- **الوصف**: تنفيذ أمر بشكل متزامن مع التحكم في الإدخال/الإخراج.
- **مثال:**
```js
const { spawnSync } = require('child_process');
const result = spawnSync('ls', ['-lh', '/usr'], { encoding: 'utf8' });
console.log('المخرجات:', result.stdout);
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/child_process.html#child_processspawnsynccommand-args-options)

---

### child_process.fork(modulePath[, args][, options])
- **modulePath**: مسار ملف جافاسكريبت (String)
- **args**: مصفوفة بارامترس (Array, اختياري)
- **options**: خيارات إضافية { cwd, env, execPath, execArgv, stdio, detached } (Object, اختياري)
- **الوصف**: تنفيذ نص برمجي كعملية فرعية (يُستخدم مع node غالباً).
- **مثال:**
```js
const { fork } = require('child_process');
const child = fork('worker.js', ['--mode', 'test']);
child.on('message', (msg) => {
  console.log('رسالة من العملية الفرعية:', msg);
});
child.send({ hello: 'world' });
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/child_process.html#child_processforkmodulepath-args-options)

---

## حالات الاستخدام الشائعة
- تنفيذ أوامر النظام من تطبيق Node.js
- بناء خطوط معالجة (pipelines) بين العمليات
- تشغيل سكريبتات أو برامج خارجية
- بناء أنظمة مراقبة أو إدارة عمليات

---

## أفضل الممارسات
- استخدم spawn مع مصفوفة بارامترس لتجنب حقن الأوامر
- استمع دائماً للأحداث ('error', 'close') وعالج الأخطاء
- حدد maxBuffer في exec/execSync إذا كنت تتوقع مخرجات كبيرة
- استخدم stdio: 'inherit' في spawn/spawnSync لتمرير الإدخال/الإخراج للطرفية
- تجنب الدوال المتزامنة (Sync) في بيئة الخوادم

---

## التحذيرات الأمنية
- لا تمرر مدخلات المستخدم مباشرة إلى exec أو shell
- راقب المخرجات الكبيرة لتفادي امتلاء الذاكرة
- لا تستخدم الدوال المتزامنة في تطبيقات الخادم

---

## أدوات التصحيح المتعلقة
- [node --inspect](https://nodejs.org/en/docs/guides/debugging-getting-started/)
- [pm2](https://pm2.keymetrics.io/) (لإدارة العمليات والمراقبة)

---

## اختبار تفاعلي
```js
const test = require('node:test');
const assert = require('node:assert');
const { exec } = require('child_process');

test('exec يعمل بدون خطأ', (t, done) => {
  exec('node -v', (error, stdout, stderr) => {
    assert.ok(stdout.includes('v'));
    done();
  });
});
```

---

## نصائح الخبراء
- استخدم الأحداث ('exit', 'close', 'error') لمراقبة حالة العملية الفرعية
- استخدم fork لتشغيل عمليات Node.js فرعية والتواصل بينها عبر الرسائل (IPC)
- لا تمرر مدخلات المستخدم مباشرة إلى exec أو shell
- استخدم stdio: 'inherit' لتمرير الإدخال/الإخراج للطرفية

---

## ملاحظات تقنية
- الدوال المتزامنة (Sync) تحجب الـ event loop وتؤثر على الأداء
- راجع [توثيق Node.js الرسمي - child_process](https://nodejs.org/docs/latest/api/child_process.html) لأي تحديثات 