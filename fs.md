### fs (نظام الملفات)
**الوصف**: 
موديول fs هو واجهة Node.js للتعامل مع نظام الملفات (قراءة، كتابة، حذف، إدارة الملفات والمجلدات). يُستخدم بكثرة في تطبيقات الخوادم لمعالجة الملفات النصية، الصور، السجلات، ويدعم العمليات المتزامنة (Sync) وغير المتزامنة (Async) عبر Callbacks وPromises.

#### جميع الدوال غير المهملة:

```javascript
// فحص صلاحيات الوصول للملف
fs.access(path[, mode], callback)
// البارامترس:
// - path: (string | Buffer | URL) مسار الملف
// - mode: (integer) صلاحيات الوصول (fs.constants.F_OK, R_OK, W_OK, X_OK)
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: فحص إمكانية قراءة ملف
fs.access('data.txt', fs.constants.R_OK, (err) => {
  if (err) console.log('لا يمكن قراءة الملف');
  else console.log('يمكن قراءة الملف');
});

// إضافة محتوى إلى نهاية ملف
fs.appendFile(path, data[, options], callback)
// البارامترس:
// - path: (string | Buffer | URL) مسار الملف
// - data: (string | Buffer) البيانات المراد إضافتها
// - options: (object | string) خيارات الترميز أو { encoding, mode, flag }
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: إضافة سجل جديد
fs.appendFile('logs.txt', '\n' + new Date().toISOString(), 'utf8', (err) => {
  if (err) console.error('خطأ في الإضافة:', err);
});

// تغيير صلاحيات الملف
fs.chmod(path, mode, callback)
// البارامترس:
// - path: (string | Buffer | URL) مسار الملف
// - mode: (integer) الصلاحيات الجديدة (مثل 0o755 للتنفيذ)
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: جعل الملف قابل للقراءة والكتابة والتنفيذ
fs.chmod('script.sh', 0o755, (err) => {
  if (err) console.error('خطأ في تغيير الصلاحيات:', err);
});

// تغيير مالك الملف
fs.chown(path, uid, gid, callback)
// البارامترس:
// - path: (string | Buffer | URL) مسار الملف
// - uid: (integer) معرف المستخدم الجديد
// - gid: (integer) معرف المجموعة الجديدة
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: تغيير مالك الملف
fs.chown('file.txt', 1000, 1000, (err) => {
  if (err) console.error('خطأ في تغيير المالك:', err);
});

// إغلاق file descriptor
fs.close(fd[, callback])
// البارامترس:
// - fd: (integer) معرف الملف المفتوح
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: إغلاق ملف مفتوح
fs.open('file.txt', 'r', (err, fd) => {
  if (err) return console.error(err);
  // استخدام الملف
  fs.close(fd, (err) => {
    if (err) console.error('خطأ في الإغلاق:', err);
  });
});

// نسخ ملف
fs.copyFile(src, dest[, mode], callback)
// البارامترس:
// - src: (string | Buffer | URL) مسار الملف المصدر
// - dest: (string | Buffer | URL) مسار الملف الهدف
// - mode: (integer) صلاحيات الملف الجديد (اختياري)
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: نسخ ملف مع الحفاظ على الصلاحيات
fs.copyFile('source.txt', 'backup.txt', fs.constants.COPYFILE_EXCL, (err) => {
  if (err) console.error('خطأ في النسخ:', err);
  else console.log('تم النسخ بنجاح');
});

// نسخ مجلد كامل
fs.cp(src, dest[, options], callback)
// البارامترس:
// - src: (string | Buffer | URL) مسار المجلد المصدر
// - dest: (string | Buffer | URL) مسار المجلد الهدف
// - options: (object) خيارات النسخ { recursive, force, filter }
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: نسخ مجلد مع جميع المحتويات
fs.cp('source/', 'backup/', { recursive: true, force: true }, (err) => {
  if (err) console.error('خطأ في نسخ المجلد:', err);
});

// إنشاء Stream للقراءة
fs.createReadStream(path[, options])
// البارامترس:
// - path: (string | Buffer | URL) مسار الملف
// - options: (object) خيارات Stream { encoding, start, end, highWaterMark }
// مثال: قراءة ملف كبير مع خيارات متقدمة
const readStream = fs.createReadStream('bigfile.txt', { 
  encoding: 'utf8',
  highWaterMark: 64 * 1024 // 64KB chunks
});
readStream.on('data', (chunk) => {
  console.log('قراءة جزء:', chunk.length, 'بايت');
});

// إنشاء Stream للكتابة
fs.createWriteStream(path[, options])
// البارامترس:
// - path: (string | Buffer | URL) مسار الملف
// - options: (object) خيارات Stream { encoding, flags, mode, autoClose }
// مثال: كتابة ملف كبير مع خيارات متقدمة
const writeStream = fs.createWriteStream('output.txt', {
  encoding: 'utf8',
  flags: 'a', // إضافة بدلاً من الكتابة فوق
  autoClose: true
});
writeStream.write('بداية الملف\n');

// فحص وجود ملف
fs.exists(path, callback)
// البارامترس:
// - path: (string | Buffer | URL) مسار الملف
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(exists)
// مثال: فحص وجود ملف
fs.exists('config.json', (exists) => {
  if (exists) console.log('الملف موجود');
  else console.log('الملف غير موجود');
});

// تغيير صلاحيات file descriptor
fs.fchmod(fd, mode, callback)
// البارامترس:
// - fd: (integer) معرف الملف المفتوح
// - mode: (integer) الصلاحيات الجديدة
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: تغيير صلاحيات ملف مفتوح
fs.open('file.txt', 'r', (err, fd) => {
  if (err) return console.error(err);
  fs.fchmod(fd, 0o644, (err) => {
    if (err) console.error('خطأ في تغيير الصلاحيات:', err);
    fs.close(fd);
  });
});

// تغيير مالك file descriptor
fs.fchown(fd, uid, gid, callback)
// البارامترس:
// - fd: (integer) معرف الملف المفتوح
// - uid: (integer) معرف المستخدم الجديد
// - gid: (integer) معرف المجموعة الجديدة
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: تغيير مالك ملف مفتوح
fs.open('file.txt', 'r', (err, fd) => {
  if (err) return console.error(err);
  fs.fchown(fd, 1000, 1000, (err) => {
    if (err) console.error('خطأ في تغيير المالك:', err);
    fs.close(fd);
  });
});

// مزامنة البيانات مع القرص
fs.fdatasync(fd, callback)
// البارامترس:
// - fd: (integer) معرف الملف المفتوح
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: مزامنة ملف مفتوح
fs.open('file.txt', 'w', (err, fd) => {
  if (err) return console.error(err);
  fs.write(fd, 'data', (err) => {
    if (err) return console.error(err);
    fs.fdatasync(fd, (err) => {
      if (err) console.error('خطأ في المزامنة:', err);
      fs.close(fd);
    });
  });
});

// الحصول على معلومات file descriptor
fs.fstat(fd[, options], callback)
// البارامترس:
// - fd: (integer) معرف الملف المفتوح
// - options: (object) خيارات إضافية { bigint }
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err, stats)
// مثال: الحصول على معلومات ملف مفتوح
fs.open('file.txt', 'r', (err, fd) => {
  if (err) return console.error(err);
  fs.fstat(fd, (err, stats) => {
    if (err) console.error('خطأ في الحصول على المعلومات:', err);
    else {
      console.log('حجم الملف:', stats.size);
      console.log('تاريخ الإنشاء:', stats.birthtime);
      console.log('نوع الملف:', stats.isFile() ? 'ملف' : 'مجلد');
    }
    fs.close(fd);
  });
});

// مزامنة ملف مع القرص
fs.fsync(fd, callback)
// البارامترس:
// - fd: (integer) معرف الملف المفتوح
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: مزامنة ملف مفتوح
fs.open('file.txt', 'w', (err, fd) => {
  if (err) return console.error(err);
  fs.write(fd, 'data', (err) => {
    if (err) return console.error(err);
    fs.fsync(fd, (err) => {
      if (err) console.error('خطأ في المزامنة:', err);
      fs.close(fd);
    });
  });
});

// تقليم file descriptor
fs.ftruncate(fd[, len], callback)
// البارامترس:
// - fd: (integer) معرف الملف المفتوح
// - len: (integer) الحجم الجديد للملف (اختياري)
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: تقليم ملف مفتوح
fs.open('file.txt', 'r+', (err, fd) => {
  if (err) return console.error(err);
  fs.ftruncate(fd, 100, (err) => {
    if (err) console.error('خطأ في التقليم:', err);
    fs.close(fd);
  });
});

// تغيير أوقات الوصول لـ file descriptor
fs.futimes(fd, atime, mtime, callback)
// البارامترس:
// - fd: (integer) معرف الملف المفتوح
// - atime: (number | Date | string) وقت آخر وصول
// - mtime: (number | Date | string) وقت آخر تعديل
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: تغيير أوقات الوصول
fs.open('file.txt', 'r+', (err, fd) => {
  if (err) return console.error(err);
  const now = new Date();
  fs.futimes(fd, now, now, (err) => {
    if (err) console.error('خطأ في تغيير الأوقات:', err);
    fs.close(fd);
  });
});

// البحث عن ملفات باستخدام نمط
fs.glob(pattern[, options], callback)
// البارامترس:
// - pattern: (string) نمط البحث (مثل '*.txt')
// - options: (object) خيارات البحث { cwd, ignore, dot }
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err, files)
// مثال: البحث عن ملفات مع خيارات متقدمة
fs.glob('**/*.txt', { 
  ignore: ['node_modules/**'],
  dot: false 
}, (err, files) => {
  if (err) console.error('خطأ في البحث:', err);
  else console.log('الملفات المطابقة:', files);
});

// تغيير صلاحيات symbolic link
fs.lchmod(path, mode, callback)
// البارامترس:
// - path: (string | Buffer | URL) مسار الرابط الرمزي
// - mode: (integer) الصلاحيات الجديدة
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: تغيير صلاحيات رابط رمزي
fs.lchmod('symlink.txt', 0o644, (err) => {
  if (err) console.error('خطأ في تغيير الصلاحيات:', err);
});

// تغيير مالك symbolic link
fs.lchown(path, uid, gid, callback)
// البارامترس:
// - path: (string | Buffer | URL) مسار الرابط الرمزي
// - uid: (integer) معرف المستخدم الجديد
// - gid: (integer) معرف المجموعة الجديدة
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: تغيير مالك رابط رمزي
fs.lchown('symlink.txt', 1000, 1000, (err) => {
  if (err) console.error('خطأ في تغيير المالك:', err);
});

// تغيير أوقات الوصول لـ symbolic link
fs.lutimes(path, atime, mtime, callback)
// البارامترس:
// - path: (string | Buffer | URL) مسار الرابط الرمزي
// - atime: (number | Date | string) وقت آخر وصول
// - mtime: (number | Date | string) وقت آخر تعديل
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: تغيير أوقات الوصول لرابط رمزي
const now = new Date();
fs.lutimes('symlink.txt', now, now, (err) => {
  if (err) console.error('خطأ في تغيير الأوقات:', err);
});

// إنشاء hard link
fs.link(existingPath, newPath, callback)
// البارامترس:
// - existingPath: (string | Buffer | URL) مسار الملف الأصلي
// - newPath: (string | Buffer | URL) مسار الرابط الجديد
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: إنشاء رابط صلب
fs.link('original.txt', 'link.txt', (err) => {
  if (err) console.error('خطأ في إنشاء الرابط:', err);
});

// الحصول على معلومات symbolic link
fs.lstat(path[, options], callback)
// البارامترس:
// - path: (string | Buffer | URL) مسار الرابط الرمزي
// - options: (object) خيارات إضافية { bigint }
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err, stats)
// مثال: الحصول على معلومات رابط رمزي
fs.lstat('symlink.txt', (err, stats) => {
  if (err) console.error('خطأ في الحصول على المعلومات:', err);
  else {
    console.log('نوع الملف:', stats.isSymbolicLink() ? 'رابط رمزي' : 'ملف عادي');
    console.log('حجم الملف:', stats.size);
  }
});

// إنشاء مجلد
fs.mkdir(path[, options], callback)
// البارامترس:
// - path: (string | Buffer | URL) مسار المجلد الجديد
// - options: (object) خيارات الإنشاء { recursive, mode }
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: إنشاء مجلد مع خيارات متقدمة
fs.mkdir('newfolder', { 
  recursive: true, 
  mode: 0o755 
}, (err) => {
  if (err) console.error('خطأ في إنشاء المجلد:', err);
  else console.log('تم إنشاء المجلد');
});

// إنشاء مجلد مؤقت
fs.mkdtemp(prefix[, options], callback)
// البارامترس:
// - prefix: (string) البادئة لاسم المجلد المؤقت
// - options: (object) خيارات إضافية { encoding }
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err, folderPath)
// مثال: إنشاء مجلد مؤقت
fs.mkdtemp('temp-', (err, folder) => {
  if (err) console.error('خطأ في إنشاء المجلد المؤقت:', err);
  else console.log('المجلد المؤقت:', folder);
});

// فتح ملف
fs.open(path, flags[, mode], callback)
// البارامترس:
// - path: (string | Buffer | URL) مسار الملف
// - flags: (string) وضعية الفتح ('r', 'w', 'a', 'r+', 'w+', 'a+')
// - mode: (integer) صلاحيات الملف (افتراضي 0o666)
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err, fd)
// مثال: فتح ملف للقراءة
fs.open('file.txt', 'r', (err, fd) => {
  if (err) return console.error(err);
  console.log('تم فتح الملف، الـ descriptor:', fd);
  fs.close(fd);
});

// فتح ملف كـ Blob
fs.openAsBlob(path[, options])
// البارامترس:
// - path: (string | Buffer | URL) مسار الملف
// - options: (object) خيارات إضافية { type }
// مثال: فتح ملف كـ Blob
const blob = fs.openAsBlob('image.jpg', { type: 'image/jpeg' });
console.log('نوع الملف:', blob.type);

// فتح مجلد
fs.opendir(path[, options], callback)
// البارامترس:
// - path: (string | Buffer | URL) مسار المجلد
// - options: (object) خيارات إضافية { encoding, bufferSize }
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err, dir)
// مثال: فتح مجلد للقراءة
fs.opendir('./', { bufferSize: 64 }, (err, dir) => {
  if (err) return console.error(err);
  dir.read((err, dirent) => {
    if (err) console.error(err);
    else if (dirent) {
      console.log('اسم الملف:', dirent.name);
      console.log('نوع الملف:', dirent.isFile() ? 'ملف' : 'مجلد');
    }
    dir.close();
  });
});

// قراءة من file descriptor
fs.read(fd, buffer, offset, length, position, callback)
// البارامترس:
// - fd: (integer) معرف الملف المفتوح
// - buffer: (Buffer | TypedArray | DataView) buffer للقراءة إليه
// - offset: (integer) موقع البداية في الـ buffer
// - length: (integer) عدد البايتات المراد قراءتها
// - position: (integer) موقع البداية في الملف
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err, bytesRead, buffer)
// مثال: قراءة من ملف مفتوح
const buffer = Buffer.alloc(1024);
fs.open('file.txt', 'r', (err, fd) => {
  if (err) return console.error(err);
  fs.read(fd, buffer, 0, 1024, 0, (err, bytesRead, buffer) => {
    if (err) console.error('خطأ في القراءة:', err);
    else {
      console.log('تم قراءة', bytesRead, 'بايت');
      console.log('المحتوى:', buffer.toString('utf8', 0, bytesRead));
    }
    fs.close(fd);
  });
});

// قراءة مجلد
fs.readdir(path[, options], callback)
// البارامترس:
// - path: (string | Buffer | URL) مسار المجلد
// - options: (object | string) خيارات القراءة { encoding, withFileTypes }
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err, files)
// مثال: قراءة محتويات مجلد مع تفاصيل
fs.readdir('./', { 
  encoding: 'utf8',
  withFileTypes: true 
}, (err, files) => {
  if (err) console.error('خطأ في قراءة المجلد:', err);
  else {
    files.forEach(file => {
      console.log('اسم:', file.name, 'نوع:', file.isFile() ? 'ملف' : 'مجلد');
    });
  }
});

// قراءة ملف
fs.readFile(path[, options], callback)
// البارامترس:
// - path: (string | Buffer | URL) مسار الملف
// - options: (object | string) خيارات القراءة { encoding, flag, signal }
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err, data)
// مثال: قراءة ملف نصي مع خيارات متقدمة
fs.readFile('data.txt', { 
  encoding: 'utf8',
  flag: 'r'
}, (err, data) => {
  if (err) console.error('خطأ في قراءة الملف:', err);
  else console.log('محتوى الملف:', data);
});

// قراءة symbolic link
fs.readlink(path[, options], callback)
// البارامترس:
// - path: (string | Buffer | URL) مسار الرابط الرمزي
// - options: (object) خيارات إضافية { encoding }
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err, linkString)
// مثال: قراءة رابط رمزي
fs.readlink('symlink.txt', (err, linkString) => {
  if (err) console.error('خطأ في قراءة الرابط:', err);
  else console.log('الرابط يشير إلى:', linkString);
});

// قراءة متعددة من file descriptor
fs.readv(fd, buffers[, position], callback)
// البارامترس:
// - fd: (integer) معرف الملف المفتوح
// - buffers: (Array) مصفوفة من الـ buffers
// - position: (integer) موقع البداية في الملف
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err, bytesRead, buffers)
// مثال: قراءة متعددة
const buffers = [Buffer.alloc(10), Buffer.alloc(10)];
fs.open('file.txt', 'r', (err, fd) => {
  if (err) return console.error(err);
  fs.readv(fd, buffers, 0, (err, bytesRead, buffers) => {
    if (err) console.error('خطأ في القراءة المتعددة:', err);
    else console.log('تم قراءة', bytesRead, 'بايت');
    fs.close(fd);
  });
});

// الحصول على المسار الحقيقي
fs.realpath(path[, options], callback)
// البارامترس:
// - path: (string | Buffer | URL) مسار الملف
// - options: (object) خيارات إضافية { encoding }
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err, resolvedPath)
// مثال: الحصول على المسار الحقيقي
fs.realpath('symlink.txt', (err, resolvedPath) => {
  if (err) console.error('خطأ في حل المسار:', err);
  else console.log('المسار الحقيقي:', resolvedPath);
});

// إعادة تسمية ملف
fs.rename(oldPath, newPath, callback)
// البارامترس:
// - oldPath: (string | Buffer | URL) المسار القديم
// - newPath: (string | Buffer | URL) المسار الجديد
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: إعادة تسمية ملف
fs.rename('oldname.txt', 'newname.txt', (err) => {
  if (err) console.error('خطأ في إعادة التسمية:', err);
  else console.log('تم إعادة التسمية');
});

// حذف مجلد
fs.rmdir(path[, options], callback)
// البارامترس:
// - path: (string | Buffer | URL) مسار المجلد
// - options: (object) خيارات إضافية { recursive, maxRetries, retryDelay }
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: حذف مجلد فارغ
fs.rmdir('emptyfolder', (err) => {
  if (err) console.error('خطأ في حذف المجلد:', err);
  else console.log('تم حذف المجلد');
});

// حذف ملف أو مجلد
fs.rm(path[, options], callback)
// البارامترس:
// - path: (string | Buffer | URL) مسار الملف أو المجلد
// - options: (object) خيارات إضافية { recursive, force, maxRetries }
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: حذف ملف
fs.rm('file.txt', { force: true }, (err) => {
  if (err) console.error('خطأ في حذف الملف:', err);
  else console.log('تم حذف الملف');
});

// الحصول على معلومات ملف
fs.stat(path[, options], callback)
// البارامترس:
// - path: (string | Buffer | URL) مسار الملف
// - options: (object) خيارات إضافية { bigint }
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err, stats)
// مثال: الحصول على معلومات ملف
fs.stat('file.txt', (err, stats) => {
  if (err) console.error('خطأ في الحصول على المعلومات:', err);
  else {
    console.log('حجم الملف:', stats.size, 'بايت');
    console.log('تاريخ الإنشاء:', stats.birthtime);
    console.log('تاريخ التعديل:', stats.mtime);
    console.log('نوع الملف:', stats.isFile() ? 'ملف' : 'مجلد');
    console.log('صلاحيات الملف:', stats.mode.toString(8));
  }
});

// الحصول على معلومات نظام الملفات
fs.statfs(path[, options], callback)
// البارامترس:
// - path: (string | Buffer | URL) مسار في نظام الملفات
// - options: (object) خيارات إضافية { bigint }
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err, stats)
// مثال: الحصول على معلومات نظام الملفات
fs.statfs('/', (err, stats) => {
  if (err) console.error('خطأ في الحصول على معلومات النظام:', err);
  else {
    console.log('المساحة المتاحة:', stats.bavail, 'كتلة');
    console.log('المساحة الكلية:', stats.blocks, 'كتلة');
    console.log('حجم الكتلة:', stats.bsize, 'بايت');
    console.log('نوع نظام الملفات:', stats.type);
  }
});

// إنشاء symbolic link
fs.symlink(target, path[, type], callback)
// البارامترس:
// - target: (string | Buffer | URL) المسار المستهدف
// - path: (string | Buffer | URL) مسار الرابط الجديد
// - type: (string) نوع الرابط ('file', 'dir', 'junction')
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: إنشاء رابط رمزي
fs.symlink('target.txt', 'link.txt', 'file', (err) => {
  if (err) console.error('خطأ في إنشاء الرابط:', err);
  else console.log('تم إنشاء الرابط الرمزي');
});

// تقليم ملف
fs.truncate(path[, len], callback)
// البارامترس:
// - path: (string | Buffer | URL) مسار الملف
// - len: (integer) الحجم الجديد للملف (اختياري)
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: تقليم ملف
fs.truncate('file.txt', 100, (err) => {
  if (err) console.error('خطأ في التقليم:', err);
  else console.log('تم تقليم الملف');
});

// حذف ملف
fs.unlink(path, callback)
// البارامترس:
// - path: (string | Buffer | URL) مسار الملف
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: حذف ملف
fs.unlink('file.txt', (err) => {
  if (err) console.error('خطأ في حذف الملف:', err);
  else console.log('تم حذف الملف');
});

// إيقاف مراقبة ملف
fs.unwatchFile(filename[, listener])
// البارامترس:
// - filename: (string | Buffer) مسار الملف
// - listener: (function) دالة المراقبة (اختياري)
// مثال: إيقاف مراقبة ملف
fs.unwatchFile('file.txt');

// تغيير أوقات الوصول
fs.utimes(path, atime, mtime, callback)
// البارامترس:
// - path: (string | Buffer | URL) مسار الملف
// - atime: (number | Date | string) وقت آخر وصول
// - mtime: (number | Date | string) وقت آخر تعديل
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: تغيير أوقات الوصول
const now = new Date();
fs.utimes('file.txt', now, now, (err) => {
  if (err) console.error('خطأ في تغيير الأوقات:', err);
});

// مراقبة ملف
fs.watch(filename[, options][, listener])
// البارامترس:
// - filename: (string | Buffer) مسار الملف أو المجلد
// - options: (object) خيارات المراقبة { persistent, recursive, encoding }
// - listener: (function) دالة المراقبة: function(eventType, filename)
// مثال: مراقبة تغييرات ملف
fs.watch('file.txt', { persistent: true }, (eventType, filename) => {
  console.log('حدث:', eventType, 'في الملف:', filename);
});

// مراقبة ملف مع خيارات
fs.watchFile(filename[, options], listener)
// البارامترس:
// - filename: (string | Buffer) مسار الملف
// - options: (object) خيارات المراقبة { interval, persistent }
// - listener: (function) دالة المراقبة: function(current, previous)
// مثال: مراقبة ملف مع خيارات
fs.watchFile('file.txt', { interval: 1000 }, (curr, prev) => {
  console.log('تغير الملف من', prev.size, 'إلى', curr.size, 'بايت');
});

// كتابة إلى file descriptor
fs.write(fd, buffer, offset[, length[, position]], callback)
// البارامترس:
// - fd: (integer) معرف الملف المفتوح
// - buffer: (Buffer | TypedArray | DataView) البيانات المراد كتابتها
// - offset: (integer) موقع البداية في الـ buffer
// - length: (integer) عدد البايتات المراد كتابتها (اختياري)
// - position: (integer) موقع الكتابة في الملف (اختياري)
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err, bytesWritten, buffer)
// مثال: كتابة إلى ملف مفتوح
const buffer = Buffer.from('Hello World');
fs.open('file.txt', 'w', (err, fd) => {
  if (err) return console.error(err);
  fs.write(fd, buffer, 0, buffer.length, 0, (err, bytesWritten, buffer) => {
    if (err) console.error('خطأ في الكتابة:', err);
    else console.log('تم كتابة', bytesWritten, 'بايت');
    fs.close(fd);
  });
});

// كتابة ملف
fs.writeFile(file, data[, options], callback)
// البارامترس:
// - file: (string | Buffer | URL | integer) مسار الملف أو file descriptor
// - data: (string | Buffer | TypedArray | DataView) البيانات المراد كتابتها
// - options: (object | string) خيارات الكتابة { encoding, mode, flag, signal }
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err)
// مثال: كتابة ملف مع خيارات متقدمة
fs.writeFile('output.txt', 'Hello World!', { 
  encoding: 'utf8',
  mode: 0o644,
  flag: 'w'
}, (err) => {
  if (err) console.error('خطأ في الكتابة:', err);
  else console.log('تمت الكتابة بنجاح');
});

// كتابة متعددة إلى file descriptor
fs.writev(fd, buffers[, position], callback)
// البارامترس:
// - fd: (integer) معرف الملف المفتوح
// - buffers: (Array) مصفوفة من الـ buffers
// - position: (integer) موقع الكتابة في الملف (اختياري)
// - callback: (function) دالة تُستدعى بعد الانتهاء: function(err, bytesWritten, buffers)
// مثال: كتابة متعددة
const buffers = [Buffer.from('Hello'), Buffer.from('World')];
fs.open('file.txt', 'w', (err, fd) => {
  if (err) return console.error(err);
  fs.writev(fd, buffers, 0, (err, bytesWritten, buffers) => {
    if (err) console.error('خطأ في الكتابة المتعددة:', err);
    else console.log('تم كتابة', bytesWritten, 'بايت');
    fs.close(fd);
  });
});
```

#### الأخطاء الشائعة:
- [❌] نسيان إغلاق file descriptors → الحل: استخدم دائمًا fs.close أو fileHandle.close().
- [❌] عدم معالجة الأخطاء في callbacks → الحل: تحقق دائماً من وجود خطأ قبل معالجة النتيجة.
- [❌] استخدام الدوال المتزامنة في تطبيقات الخادم → الحل: استخدم الدوال غير المتزامنة دائماً في بيئة الإنتاج.
- [❌] عدم التحقق من صلاحيات الوصول → الحل: استخدم fs.access قبل العمليات الحساسة.

#### نصائح الخبراء:
- [💡] استخدم Promises أو Async/Await لكتابة كود أكثر وضوحاً وأقل عرضة للأخطاء.
- [⚠️] لا تعرض مسارات الملفات الحساسة للمستخدم النهائي.
- [🚀] استخدم Streams للملفات الكبيرة لتقليل استهلاك الذاكرة وتحسين الأداء.
- [💡] أغلق جميع file descriptors بعد الانتهاء لتجنب تسرب الذاكرة.
- [🚀] رتب العمليات المتسلسلة (مثل rename ثم stat) باستخدام await أو داخل callback لتجنب السباق.
- [⚠️] استخدم fs.access بدلاً من fs.exists للتحقق من صلاحيات الوصول.
- [💡] استخدم fs.promises للدوال الحديثة التي تدعم Promises بشكل أفضل.
- [🚀] استخدم highWaterMark في Streams لتحسين الأداء مع الملفات الكبيرة.
- [⚠️] تحقق من وجود الملف قبل العمليات الحساسة لتجنب الأخطاء.
- [💡] استخدم try-catch مع الدوال المتزامنة و error handling مع الدوال غير المتزامنة. 