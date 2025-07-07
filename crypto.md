### crypto (التشفير والتجزئة في Node.js)
**الوصف**:
موديول crypto يوفر وظائف تشفير وتجزئة قوية في Node.js، مثل توليد القيم العشوائية، التحقق من سلامة البيانات، تشفير/فك تشفير، توقيع رقمي، وتوليد مفاتيح. يُستخدم في حماية كلمات المرور، التحقق من الهوية، تشفير البيانات، وبناء بروتوكولات آمنة.

#### جميع الدوال غير المهملة:

```javascript
// توليد بايتات عشوائية آمنة
crypto.randomBytes(size[, callback])
// البارامترس:
// - size: (integer) عدد البايتات المطلوبة
// - callback: (function) دالة تُستدعى بعد التوليد: function(err, buffer)
// مثال:
const crypto = require('node:crypto');
crypto.randomBytes(16, (err, buf) => {
  if (err) throw err;
  console.log('Token:', buf.toString('hex'));
});

// توليد رقم عشوائي آمن
crypto.randomInt([min, ]max[, callback])
// البارامترس:
// - min: (integer) الحد الأدنى (افتراضي 0)
// - max: (integer) الحد الأعلى (حصري)
// - callback: (function) دالة تُستدعى بعد التوليد: function(err, n)
// مثال:
crypto.randomInt(1, 100, (err, n) => {
  if (err) throw err;
  console.log('Random:', n);
});

// توليد UUID v4 عشوائي
crypto.randomUUID([options])
// البارامترس:
// - options: (object) خيارات إضافية (نادراً ما تُستخدم)
// مثال:
const uuid = crypto.randomUUID();
console.log('UUID:', uuid);

// دوال التجزئة (Hash)
crypto.createHash(algorithm)
// البارامترس:
// - algorithm: (string) اسم الخوارزمية ('sha256', 'md5', ...)
// مثال:
const hash = crypto.createHash('sha256').update('secret').digest('hex');
console.log('Hash:', hash);

// دوال HMAC (توقيع تجزئة)
crypto.createHmac(algorithm, key)
// البارامترس:
// - algorithm: (string) اسم الخوارزمية
// - key: (string | Buffer) المفتاح السري
// مثال:
const hmac = crypto.createHmac('sha256', 'key').update('data').digest('hex');
console.log('HMAC:', hmac);

// تشفير البيانات
crypto.createCipheriv(algorithm, key, iv)
// البارامترس:
// - algorithm: (string) اسم الخوارزمية (مثل 'aes-256-cbc')
// - key: (Buffer | string) مفتاح التشفير
// - iv: (Buffer | string) متجه التهيئة (Initialization Vector)
// مثال:
const cipher = crypto.createCipheriv('aes-256-cbc', key, iv);
let encrypted = cipher.update('secret', 'utf8', 'hex');
encrypted += cipher.final('hex');
console.log('Encrypted:', encrypted);

// فك التشفير
crypto.createDecipheriv(algorithm, key, iv)
// البارامترس:
// - algorithm: (string) اسم الخوارزمية
// - key: (Buffer | string) مفتاح التشفير
// - iv: (Buffer | string) متجه التهيئة
// مثال:
const decipher = crypto.createDecipheriv('aes-256-cbc', key, iv);
let decrypted = decipher.update(encrypted, 'hex', 'utf8');
decrypted += decipher.final('utf8');
console.log('Decrypted:', decrypted);

// توليد مفتاح مشتق من كلمة مرور (PBKDF2)
crypto.pbkdf2(password, salt, iterations, keylen, digest, callback)
// البارامترس:
// - password: (string | Buffer) كلمة المرور
// - salt: (string | Buffer) ملح التشفير
// - iterations: (integer) عدد التكرارات
// - keylen: (integer) طول المفتاح الناتج
// - digest: (string) خوارزمية التجزئة
// - callback: (function) دالة تُستدعى بعد التوليد: function(err, derivedKey)
// مثال:
crypto.pbkdf2('password', 'salt', 100000, 32, 'sha256', (err, key) => {
  if (err) throw err;
  console.log('Derived key:', key.toString('hex'));
});

// توليد مفتاح مشتق من كلمة مرور (scrypt)
crypto.scrypt(password, salt, keylen[, options], callback)
// البارامترس:
// - password: (string | Buffer) كلمة المرور
// - salt: (string | Buffer) ملح التشفير
// - keylen: (integer) طول المفتاح الناتج
// - options: (object) خيارات إضافية
// - callback: (function) دالة تُستدعى بعد التوليد: function(err, derivedKey)
// مثال:
crypto.scrypt('password', 'salt', 32, (err, key) => {
  if (err) throw err;
  console.log('Scrypt key:', key.toString('hex'));
});

// التحقق من صحة التوقيع الرقمي
crypto.verify(algorithm, data, key, signature)
// البارامترس:
// - algorithm: (string) اسم الخوارزمية
// - data: (Buffer | string | object) البيانات
// - key: (object | string | Buffer) المفتاح العام
// - signature: (Buffer | string) التوقيع
// مثال:
const isValid = crypto.verify('sha256', Buffer.from('data'), publicKey, signature);
console.log('Valid:', isValid);

// توقيع البيانات رقمياً
crypto.sign(algorithm, data, key)
// البارامترس:
// - algorithm: (string) اسم الخوارزمية
// - data: (Buffer | string | object) البيانات
// - key: (object | string | Buffer) المفتاح الخاص
// مثال:
const signature = crypto.sign('sha256', Buffer.from('data'), privateKey);
console.log('Signature:', signature.toString('hex'));

// مقارنة آمنة بين بافرين (لمنع هجمات التوقيت)
crypto.timingSafeEqual(a, b)
// البارامترس:
// - a, b: (Buffer) البافرين للمقارنة
// مثال:
const isEqual = crypto.timingSafeEqual(Buffer.from('a'), Buffer.from('a'));
console.log('Equal:', isEqual);

// الحصول على قائمة الخوارزميات المدعومة
crypto.getHashes()
// مثال:
console.log('Available hashes:', crypto.getHashes());

// الحصول على قائمة خوارزميات التشفير المدعومة
crypto.getCiphers()
// مثال:
console.log('Available ciphers:', crypto.getCiphers());
```

#### الأخطاء الشائعة:
- [❌] استخدام خوارزميات ضعيفة أو قديمة (مثل md5 أو sha1) → الحل: استخدم sha256 أو أقوى.
- [❌] تمرير مدخلات المستخدم مباشرة إلى التشفير أو التوقيع → الحل: تحقق وفلتر المدخلات دائماً.
- [❌] استخدام مفاتيح أو IV غير عشوائية أو قصيرة → الحل: استخدم randomBytes لطول مناسب.
- [❌] تجاهل معالجة الأخطاء في callbacks أو try/catch → الحل: عالج جميع الأخطاء دائماً.
- [❌] تحويل بيانات ثنائية إلى نص بدون ترميز مناسب → الحل: استخدم Buffer أو base64/hex عند الحاجة.

#### نصائح الخبراء:
- [⚠️] لا تستخدم خوارزميات قديمة أو ضعيفة حتى لو كانت مدعومة.
- [💡] استخدم randomBytes أو randomInt لتوليد القيم العشوائية بدلاً من Math.random.
- [🚀] استخدم scrypt أو pbkdf2 مع عدد تكرارات كبير لتخزين كلمات المرور بأمان.
- [⚠️] لا تعرض المفاتيح أو القيم السرية في السجلات أو رسائل الخطأ.
- [💡] استخدم timingSafeEqual عند مقارنة التواقيع أو الرموز لمنع هجمات التوقيت.
- [🚀] راقب تحديثات Node.js باستمرار لتجنب الثغرات في مكتبة التشفير. 