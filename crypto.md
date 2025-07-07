# crypto (التشفير والتجزئة في Node.js)

## الوصف
موديول crypto يوفر وظائف تشفير وتجزئة قوية في Node.js، مثل توليد القيم العشوائية، التحقق من سلامة البيانات، تشفير/فك تشفير، توقيع رقمي، وتوليد مفاتيح. يُستخدم في حماية كلمات المرور، التحقق من الهوية، تشفير البيانات، وبناء بروتوكولات آمنة.

---

## فهرس الدوال والكلاسات
| الدالة/الكلاس | الوصف |
|---------------|-------|
| [`crypto.randomBytes`](#cryptorandombytessize-callback) | توليد بايتات عشوائية آمنة |
| [`crypto.randomInt`](#cryptorandomintmin-max-callback) | توليد رقم عشوائي آمن |
| [`crypto.randomUUID`](#cryptorandomuuidoptions) | توليد UUID v4 عشوائي |
| [`crypto.createHash`](#cryptocreatehashalgorithm) | إنشاء كائن تجزئة |
| [`crypto.createHmac`](#cryptocreatehmacalgorithm-key) | إنشاء كائن HMAC |
| [`crypto.createCipheriv`](#cryptocreatecipherivalgorithm-key-iv) | تشفير البيانات |
| [`crypto.createDecipheriv`](#cryptocreatedecipherivalgorithm-key-iv) | فك التشفير |
| [`crypto.pbkdf2`](#cryptopbkdf2password-salt-iterations-keylen-digest-callback) | توليد مفتاح مشتق (PBKDF2) |
| [`crypto.scrypt`](#cryptoscryptpassword-salt-keylen-options-callback) | توليد مفتاح مشتق (scrypt) |
| [`crypto.verify`](#cryptoverifyalgorithm-data-key-signature) | التحقق من صحة التوقيع الرقمي |
| [`crypto.sign`](#cryptosignalorithm-data-key) | توقيع البيانات رقمياً |
| [`crypto.timingSafeEqual`](#cryptotimingsafeequala-b) | مقارنة آمنة بين بافرين |
| [`crypto.getHashes`](#cryptogethashes) | قائمة خوارزميات التجزئة |
| [`crypto.getCiphers`](#cryptogetciphers) | قائمة خوارزميات التشفير |

---

## شرح الدوال والكلاسات الأساسية

### crypto.randomBytes(size[, callback])
- **size**: عدد البايتات المطلوبة (Integer)
- **callback**: دالة تُستدعى بعد التوليد (اختياري)
- **الوصف**: توليد بايتات عشوائية آمنة.
- **مثال:**
```js
const crypto = require('node:crypto');
crypto.randomBytes(16, (err, buf) => {
  if (err) throw err;
  console.log('Token:', buf.toString('hex'));
});
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/crypto.html#cryptorandombytessize-callback)

---

### crypto.randomInt([min, ]max[, callback])
- **min**: الحد الأدنى (افتراضي 0)
- **max**: الحد الأعلى (حصري)
- **callback**: دالة تُستدعى بعد التوليد (اختياري)
- **الوصف**: توليد رقم عشوائي آمن.
- **مثال:**
```js
crypto.randomInt(1, 100, (err, n) => {
  if (err) throw err;
  console.log('Random:', n);
});
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/crypto.html#cryptorandomintmin-max-callback)

---

### crypto.randomUUID([options])
- **options**: خيارات إضافية (نادراً ما تُستخدم)
- **الوصف**: توليد UUID v4 عشوائي.
- **مثال:**
```js
const uuid = crypto.randomUUID();
console.log('UUID:', uuid);
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/crypto.html#cryptorandomuuidoptions)

---

### crypto.createHash(algorithm)
- **algorithm**: اسم الخوارزمية ('sha256', 'md5', ...)
- **الوصف**: إنشاء كائن تجزئة (Hash).
- **مثال:**
```js
const hash = crypto.createHash('sha256').update('secret').digest('hex');
console.log('Hash:', hash);
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/crypto.html#cryptocreatehashalgorithm-options)

---

### crypto.createHmac(algorithm, key)
- **algorithm**: اسم الخوارزمية
- **key**: المفتاح السري (String | Buffer)
- **الوصف**: إنشاء كائن HMAC (توقيع تجزئة).
- **مثال:**
```js
const hmac = crypto.createHmac('sha256', 'key').update('data').digest('hex');
console.log('HMAC:', hmac);
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/crypto.html#cryptocreatehmacalgorithm-key-options)

---

### crypto.createCipheriv(algorithm, key, iv)
- **algorithm**: اسم الخوارزمية (مثل 'aes-256-cbc')
- **key**: مفتاح التشفير (Buffer | string)
- **iv**: متجه التهيئة (Buffer | string)
- **الوصف**: تشفير البيانات باستخدام خوارزمية متماثلة.
- **مثال:**
```js
const cipher = crypto.createCipheriv('aes-256-cbc', key, iv);
let encrypted = cipher.update('secret', 'utf8', 'hex');
encrypted += cipher.final('hex');
console.log('Encrypted:', encrypted);
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/crypto.html#cryptocreatecipherivalgorithm-key-iv-options)

---

### crypto.createDecipheriv(algorithm, key, iv)
- **algorithm**: اسم الخوارزمية
- **key**: مفتاح التشفير (Buffer | string)
- **iv**: متجه التهيئة (Buffer | string)
- **الوصف**: فك تشفير البيانات المشفرة.
- **مثال:**
```js
const decipher = crypto.createDecipheriv('aes-256-cbc', key, iv);
let decrypted = decipher.update(encrypted, 'hex', 'utf8');
decrypted += decipher.final('utf8');
console.log('Decrypted:', decrypted);
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/crypto.html#cryptocreatedecipherivalgorithm-key-iv-options)

---

### crypto.pbkdf2(password, salt, iterations, keylen, digest, callback)
- **password**: كلمة المرور (String | Buffer)
- **salt**: ملح التشفير (String | Buffer)
- **iterations**: عدد التكرارات (Integer)
- **keylen**: طول المفتاح الناتج (Integer)
- **digest**: خوارزمية التجزئة (String)
- **callback**: دالة تُستدعى بعد التوليد
- **الوصف**: توليد مفتاح مشتق من كلمة مرور باستخدام PBKDF2.
- **مثال:**
```js
crypto.pbkdf2('password', 'salt', 100000, 32, 'sha256', (err, key) => {
  if (err) throw err;
  console.log('Derived key:', key.toString('hex'));
});
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/crypto.html#cryptopbkdf2password-salt-iterations-keylen-digest-callback)

---

### crypto.scrypt(password, salt, keylen[, options], callback)
- **password**: كلمة المرور (String | Buffer)
- **salt**: ملح التشفير (String | Buffer)
- **keylen**: طول المفتاح الناتج (Integer)
- **options**: خيارات إضافية (Object)
- **callback**: دالة تُستدعى بعد التوليد
- **الوصف**: توليد مفتاح مشتق من كلمة مرور باستخدام scrypt.
- **مثال:**
```js
crypto.scrypt('password', 'salt', 32, (err, key) => {
  if (err) throw err;
  console.log('Scrypt key:', key.toString('hex'));
});
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/crypto.html#cryptoscryptpassword-salt-keylen-options-callback)

---

### crypto.verify(algorithm, data, key, signature)
- **algorithm**: اسم الخوارزمية
- **data**: البيانات (Buffer | string | object)
- **key**: المفتاح العام (Object | String | Buffer)
- **signature**: التوقيع (Buffer | String)
- **الوصف**: التحقق من صحة التوقيع الرقمي.
- **مثال:**
```js
const isValid = crypto.verify('sha256', Buffer.from('data'), publicKey, signature);
console.log('Valid:', isValid);
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/crypto.html#cryptoverifyalgorithm-data-key-signature)

---

### crypto.sign(algorithm, data, key)
- **algorithm**: اسم الخوارزمية
- **data**: البيانات (Buffer | string | object)
- **key**: المفتاح الخاص (Object | String | Buffer)
- **الوصف**: توقيع البيانات رقمياً.
- **مثال:**
```js
const signature = crypto.sign('sha256', Buffer.from('data'), privateKey);
console.log('Signature:', signature.toString('hex'));
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/crypto.html#cryptosignalorithm-data-key)

---

### crypto.timingSafeEqual(a, b)
- **a, b**: البافرين للمقارنة (Buffer)
- **الوصف**: مقارنة آمنة بين بافرين لمنع هجمات التوقيت.
- **مثال:**
```js
const isEqual = crypto.timingSafeEqual(Buffer.from('a'), Buffer.from('a'));
console.log('Equal:', isEqual);
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/crypto.html#cryptotimingsafeequala-b)

---

### crypto.getHashes()
- **الوصف**: الحصول على قائمة الخوارزميات المدعومة للتجزئة.
- **مثال:**
```js
console.log('Available hashes:', crypto.getHashes());
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/crypto.html#cryptogethashes)

---

### crypto.getCiphers()
- **الوصف**: الحصول على قائمة خوارزميات التشفير المدعومة.
- **مثال:**
```js
console.log('Available ciphers:', crypto.getCiphers());
```
[توثيق رسمي](https://nodejs.org/docs/latest/api/crypto.html#cryptogetciphers)

---

## حالات الاستخدام الشائعة
- حماية كلمات المرور وتخزينها بأمان
- التحقق من سلامة البيانات (hash, HMAC)
- تشفير وفك تشفير البيانات الحساسة
- توليد رموز عشوائية أو UUIDs
- توقيع البيانات والتحقق من صحتها

---

## أفضل الممارسات
- استخدم خوارزميات قوية وحديثة (sha256, sha512, aes-256-cbc)
- استخدم randomBytes أو randomInt للقيم العشوائية
- استخدم scrypt أو pbkdf2 مع عدد تكرارات كبير لكلمات المرور
- عالج جميع الأخطاء في callbacks أو try/catch
- استخدم timingSafeEqual عند مقارنة التواقيع أو الرموز

---

## التحذيرات الأمنية
- لا تستخدم خوارزميات ضعيفة أو قديمة (md5, sha1)
- لا تمرر مدخلات المستخدم مباشرة إلى التشفير أو التوقيع
- لا تستخدم مفاتيح أو IV غير عشوائية أو قصيرة
- لا تعرض المفاتيح أو القيم السرية في السجلات أو رسائل الخطأ

---

## أدوات التصحيح المتعلقة
- [node --inspect](https://nodejs.org/en/docs/guides/debugging-getting-started/)
- [crypto-js](https://www.npmjs.com/package/crypto-js) (مكتبة تشفير إضافية)

---

## اختبار تفاعلي
```js
const test = require('node:test');
const assert = require('node:assert');
const crypto = require('node:crypto');

test('اختبار randomBytes', (t) => {
  crypto.randomBytes(8, (err, buf) => {
    assert.strictEqual(buf.length, 8);
  });
});
```

---

## نصائح الخبراء
- استخدم randomBytes أو randomInt بدلاً من Math.random
- استخدم scrypt أو pbkdf2 مع عدد تكرارات كبير لتخزين كلمات المرور
- استخدم timingSafeEqual عند مقارنة التواقيع أو الرموز
- راقب تحديثات Node.js باستمرار لتجنب الثغرات

---

## ملاحظات تقنية
- بعض الدوال تدعم Promises (راجع التوثيق الرسمي)
- بعض الخوارزميات قد لا تكون متاحة في جميع الأنظمة
- راجع [توثيق Node.js الرسمي - crypto](https://nodejs.org/docs/latest/api/crypto.html) لأي تحديثات 