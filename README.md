# AKT Group Office Notes AI

دفتر ملاحظات المكتب الذكي. يعمل بطريقتين من نفس الكود:
- **Cloudflare Workers + D1** (مجاني، شغّال 24 ساعة، رابط عام) — الطريقة الموصى بها.
- **محليًا على Node 22+** مع SQLite (`npm start`).

## النشر على Cloudflare (مجاني)
تحتاج حساب Cloudflare مجاني وNode 22+ على جهازك. افتح CMD/PowerShell داخل مجلد المشروع:

1. `npm install`
2. `npx wrangler login` (يفتح المتصفح لتسجيل الدخول)
3. `npx wrangler d1 create akt-notes` ← انسخ قيمة `database_id` التي تظهر وضعها في `wrangler.jsonc` مكان `REPLACE_WITH_DATABASE_ID`
4. `npm run db:migrate` (اكتب y عند السؤال) ← ينشئ الجداول في قاعدة D1
5. `npx wrangler secret put ANTHROPIC_API_KEY` ← الصق مفتاح الذكاء الاصطناعي
6. `npx wrangler secret put SETUP_CODE` ← اختر رمزًا سريًا (يمنع أي زائر من إنشاء حساب المدير قبلك)
7. `npm run deploy` ← يظهر لك الرابط `https://akt-notes.<اسمك>.workers.dev`
8. افتح الرابط، وأنشئ حساب المدير (ستُسأل عن رمز الإعداد). ثم أضف الموظفين من القائمة العلوية.

التحديث لاحقًا: عدّل الملفات ثم `npm run deploy` (والبيانات تبقى كما هي).
دومين خاص: من لوحة Cloudflare ← Workers ← akt-notes ← Settings ← Domains & Routes.

ملاحظات: الطبقة المجانية لها حدود يومية (طلبات ووقت معالجة)، وتنطبق شروط Cloudflare الحالية.
إن ظهر خطأ 1102 (تجاوز وقت المعالجة) عند تسجيل الدخول فاضبط `PBKDF2_ITERATIONS` أقل (في vars بـ wrangler.jsonc) أو استخدم خطة Workers المدفوعة.

## التشغيل المحلي
```bash
cp .env.example .env      # ضع ANTHROPIC_API_KEY
npm start                 # http://127.0.0.1:3000
npm test                  # 13 اختبارًا
```

## الذكاء الاصطناعي
المفتاح يبقى على الخادم فقط (Secret على Cloudflare أو `.env` محليًا). بدونه تعمل الملاحظات كاملة وتظهر رسالة واضحة.
لا تُرسل أي ملاحظة إلى AI إلا عند ضغط المستخدم زر الميزة أو إرسال سؤال في «اسأل ملاحظاتك».

## Docker (اختياري، لسيرفر خاص)
`docker compose up -d --build` بعد ضبط `DOMAIN` في `.env`. النسخ الاحتياطي: `./backup.sh`.
